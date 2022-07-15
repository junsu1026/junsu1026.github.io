---
layout: post
title:  "PostgreSQL Lock 정리(2)"
date:   2022-07-14T14:12:52-05:00
author: Junsu Noh
categories: PostgreSQL
tags:	jekyll welcome
cover:  "/assets/instacode.png" 
---

## Lock의 설정 범위(level)

- **데이터베이스**
    - 데이터베이스 범위의 lock은 전체 데이터베이스를 기준으로 lock 하는 것입니다. 즉, 1개의 세션만이 DB의 데이터에 접근이 가능합니다. 해당 기능은 일반적으로는 사용하지 않습니다. **사용하는 때가 있다면 DB의 소프트웨어 버전을 올린다던지 주요한 DB의 업데이트에 사용합니다.**
- **파일**
    - 데이터베이스 파일을 기준으로 lock을 설정합니다. 파일 이란 테이블, row 등과 같은 실제 데이터가 쓰여지는 물리적인 저장소 입니다. 해당 범위의 Lock은 잘 사용되지는 않습니다.
- **테이블**
    - 테이블 수준의 Lock은 테이블을 기준으로 Lock을 설정합니다. 이는 테이블의 모든 행을 업데이트 하는 등의 전체 테이블에 영향을 주는 변경을 수행할 때 유용합니다. 즉, **DDL(create, alter, drop 등) 구문과 함께 사용되며 DDL Lock이라고도 합니다.**
- **페이지와 블럭**
    - **파일의 일부인 페이지와 블록을 기준으로 Lock을 설정**합니다. 잘 사용되지는 않습니다.
- **컬럼**
    - 컬럼 기준의 Lock은 컬럼을 기준으로 Lock을 설정할 수 있습니다. 하지만 이 형식은 Lock 설정 및 해제의 리소스가 많이 들기 때문에 일반적으로 사용되지는 않습니다. 지원하는 DBMS도 많지 않습니다.
- **행(Row)**
    - **행 수준의 Lock은 1개의 행(Row)를 기준으로 Lock 설정을 합니다. DML에 대한 Lock으로 가장 일반적으로 사용하는 Lock입니다.**

## 테이블 수준의 Lock

*아래는 사용 가능한 lock 모드와 PostgreSQL에서 자동으로 사용되는 context입니다. lock 명령어를 통해 명시적으로 lock을 얻을 수 있습니다. 
아래 리스트에서 row라는 단어가 포함 되더라도 여기서 설명하는 lock 모드는 모두 테이블 수준의 lock입니다. 
이름은 각 lock 모드의 일반적인 사용법에 대한 의미를 내포하지만 테이블 수준의 lock을 거는 것은 동일합니다. 
lock의 수준에 따라 동일한 테이블에 2개 이상의 lock이 잡힐 수도 있고 없을 수 있습니다.

> Access Share

ACCESS EXCLUSIVE lock 과 충돌하고 나머지 lock과는 충돌하지 않습니다. 
보통 SELECT 명령으로 대상 테이블에서 해당 lock을 얻을 수 있습니다. 
즉, 테이블을 읽기만 하고 수정, 삭제 등을 하지 않는 쿼리에서 해당 lock을 얻습니다.

> Row Share

EXCLUSIVE와 ACCESS EXCLUSIVE lock과 충돌합니다. SELECT FOR UPDATE 와 SELECT FOR SHARE 명령으로 대상 테이블에서 해당 lock을 얻을 수 있습니다.

> Row Exclusive

SHARE, SHARE ROW EXCLUSIVE, EXCLUSIVE 및 ACCESS EXCLUSIVE 잠금 모드 와 충돌 합니다.UPDATE, DELETE 및 명령 INSERT는 대상 테이블에서 이 잠금 모드를 획득합니다( ACCESS SHARE 다른 참조 테이블에 대한 잠금 추가). 
일반적으로 이 잠금 모드는 테이블의 데이터를 수정하는 모든 명령에 의해 획득됩니다 .

> Share Update Exclusive

SHARE UPDATE EXCLUSIVE, SHARE, SHARE ROW EXCLUSIVE, EXCLUSIVE, ACCESS EXCLUSIVE lock과 충돌합니다. 해당 모드는 스키마 동시 변경 또는 VACUUM 실행으로부터 테이블을 보호합니다. 
VACUUM (FULL X) ANALYZE, CREATE INDEX CONCURRENTLY, ALTER TABLE VALIDATE 및 기타 ALTER TABLE에 의해 lock을 얻습니다.

> Share

ROW EXCLUSIVE, SHARE UPDATE EXCLUSIVE, SHARE ROW EXCLUSIVE, EXCLUSIVE 및 ACCESS EXCLUSIVE lock과 충돌합니다. 
CREATE INDEX (CONCURRENTLY 옵션 X) 에서 해당 lock을 얻을 수 있습니다.

> Share Row Exclusive

ROW EXCLUSIVE, SHARE UPDATE EXCLUSIVE, SHARE, SHARE ROW EXCLUSIVE, EXCLUSIVE 및 ACCESS EXCLUSIVE lock과 충돌합니다. 해당 모드는 동시 데이터 변경으로부터 테이블을 보호하고 한 번에 하나의 세션만 테이블을 차지할 수 있도록 처리합니다. 
CREATE TRIGGER와 다양한 형태의 ALTER TABLE에 의해 lock을 얻습니다.

> Exclusive

ROW SHARE, ROW EXCLUSIVE, SHARE UPDATE EXCLUSIVE, SHARE, SHARE ROW EXCLUSIVE, EXCLUSIVE 및 ACCESS EXCLUSIVE lock과 충돌합니다. 
해당 모드는 ACCESS SHARE lock만 허용합니다. 즉, 테이블에서 데이터를 읽고만 있는 트랜잭션이 있다면 병렬로 해당 lock을 잡을 수 있습니다.
REFRESH MATERIALIZED VIEW CONCURRENTLY(View의 옵션)에 의해 lock을 얻습니다.

> Access Exclusive

모든 lock과 충돌합니다. 해당 모드는 모든 lock과 충돌하기 때문에 일관성이 가장 유지가 되는 lock입니다. 
DROP TABLE, TRUNCATE, REINDEX, CLUSTER, VACUUM FULL, REFRESH MATERIALIZED VIEW (CONCURRENTLY 옵션 X) 명령에 의해 얻을 수 있고 많은 ALTER TABLE 명령어에서도 해당 lock을 얻을 수 있습니다. 
lock 모드를 명시적으로 지정하지 않는다면 LOCK TABLE 구문 기본인 lock입니다.

*- 일단 lock을 얻으면 트랜잭션이 끝날 때까지 유지가 됩니다. 그러나 savepoint를 설정한 다음, lock을 획득하게 되면 savepoint가 롤백될 때 lock이 해제됩니다. 
이는 ROLLBACK이 savepoint 이후의 명령을 모두 취소하는 원칙과 동일합니다.

Lock 충돌 표

![PostgreSQL Lock 충돌 표.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/31d982f6-bbb7-4d01-a08d-5e9518bc414d/PostgreSQL_Lock_충돌_표.png)
