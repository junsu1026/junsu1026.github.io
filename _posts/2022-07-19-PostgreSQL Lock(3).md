---
layout: post
title:  "PostgreSQL Lock 정리(2)"
date:   2022-07-18T14:12:52-05:00
author: Junsu Noh
categories: PostgreSQL
tags:	jekyll welcome
cover:  "/assets/instacode.png" 
---

## postgresql의 특징

- PostgreSQL UPDATE시 내부적으로는 새 행이 INSERT되고 이전 데이터는 삭제 표시가 된다. 
  모든 인덱스에는 행의 실제 위치값에 대한 링크가 표기되어 있는데, 행이 업데이트되면 변경된 위치값(물리적인 위치)에 대한 인덱스 정보도 업데이트가 필요하다.
  이런 과정 때문에 UPDATE시에는 MySQL보다 성능이 떨어진다.

## 권한

아무런 권한이 없는 계정도 pg_catalog 스키마를 읽기가 가능하다.

계정 생성 후 스키마 읽기 권한과 select 권한을 주고 select 명령어를 사용하면 lock이 잡힌다.

자기 계정 내에서는 kill하는 권한을 부여하지 않아도 가능하지만 다른 계정에 있는 lock을 kill 할때는 슈퍼유저 계정에서 

*GRANT pg_signal_backend TO 유저이름; 

을 사용해서 권한을 부여해줘야 한다. 또한 일반계정에서는 슈퍼계정의 lock을 kill 할 수 없다. 

다른 DB에 있는 lock 또한 조회,kill할 수 있다. 하지만 어떤 테이블이 lock이 잡힌것인지는 확인이 불가능하다.(어떤 lock인지는 확인 가능)


## - 락을 피하는 방법 TIP (PostgreSQL 10버전 이후)

1. Lock 때문에 트랜잭션들이 큐에 쌓이는걸 피하려면, Lock에 TimeOut을 거는게 좋다.
2. 인덱스를 추가할 때, **CONCURRENTLY 옵션을 넣어주는게 좋다.**
3. 부하가 걸릴만한 Lock은 최대한 늦게 잡는게 좋다.
4. 기존 테이블에 PK를 추가할 때는, 인덱스를 만들고, 인덱스를 PK로 거는게 좋다.
5. VACUUM FULL은 절대 쓰지 말도록 한다.
6. 데드락(교착상태)을 발생시킬만한 순서로 쿼리를 쓰지 않는게 좋다.
