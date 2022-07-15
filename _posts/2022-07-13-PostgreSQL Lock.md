---
layout: post
title:  "PostgreSQL Lock 정리"
date:   2022-07-12T14:12:52-05:00
author: Junsu Noh
categories: PostgreSQL
tags:	jekyll welcome
cover:  "/assets/instacode.png" 
---

## PostgreSQL Lock에 대한 정리

하나의 데이터베이스를 가지고 작업을 하다보니 Lock이 걸려 작업을 할 수 없는 경우가 발생했다. 
이론으로 아는 것보다 실제로 겪으니 생각보다 난처했고 해결방법을 빠르게 찾아야 했다.

## Basic 

명령어를 실행하면, 이후 실행하는 SQL명령문은 transaction으로 묶이게 되어 commit또는 rollback을 하게 될 때 까지 DB에 반영되지 않습니다. 
Postgresql에서는 pg_catalog라는 스키마에 다양한 메타정보를 관리합니다. 
그 중 pg_locks view는 database server에 현재 transaction에서 잡혀있는 lock에 대한 정보를 제공해줍니다.

## LOCK
Lock은 transaction과 너무 연관이 깊다. 
데이터베이스의 동시성과 일관성을 위해서 Lock은 꼭 필요했다.
예를 들어, 은행에서 하나의 계좌를 가지고 A,B가 각자의 ATM 기기에서 돈을 빼내려고 하는 상황에 A가 먼저 돈을 뺐다면 B는 돈을 뺄 수 없어야하고 
A가 돈을 뺀 순간 계좌에는 돈이 없어져야 한다. 만약 동시성이 없다면 A,B가 동시에 계좌를 조회를 못하게 되고 일관성이 없다면 A가 돈을 전부 빼도
B가 돈을 또 빼버릴 것이다.

Lock에는 크게 두가지가 존재하는데 

## Share Lock(공유 Lock)

다른 말로 Read lock이라고도 불린다.
일반적으로 Select문을 사용하였을 때 많이 발생하며 Exclusive lock과 충돌합니다.

## Exclusive Lock(베타 Lock)

다른 말로 Write lock이라고도 불린다.
일반적으로 테이블에 변경사항이 생길때 발생하며 Share lock, Exclusive lock과 충돌합니다.



테이블 lock 전체 확인 Query

    SELECT  t.relname,
        l.locktype,
        page,
        virtualtransaction,
        pid,
        mode,
        granted
    FROM pg_locks l,
	  pg_stat_all_tables t
    WHERE l.relation = t.relid
    ORDER BY relation ASC;



## postgresql의 특징

PostgreSQL UPDATE시 내부적으로는 새 행이 INSERT되고 이전 데이터는 삭제 표시가 된다. 모든 인덱스에는 행의 실제 위치값에 대한 링크가 표기되어 있는데, 
행이 업데이트되면 변경된 위치값(물리적인 위치)에 대한 인덱스 정보도 업데이트가 필요하다. 이런 과정 때문에 UPDATE시에는 MySQL보다 성능이 떨어진다.


## DB LOCK

> 1. 명시적 LOCK (Explicit Lock)
 
PostgreSQL은 여러 lock 모드들을 사용하여 테이블의 데이터를 동시 접근을 제어합니다. 이와 같은 lock 모드는 다중 버전 동시성 제어(MVCC)를 제공하지 않는 상황에서 어플리케이션을 제어하는 lock을 쓸 수 있고 대부분의 PostgreSQL 명령들은 해당 명령이 실행되는 동안 명령 실행의 대상이 되는 테이블이 삭제되거나 수정되지 않도록 적절한 모드의 lock을 자동으로 얻습니다. 예를 들어, 특정 테이블에서 truncate가 실행되는 동안 다른 작업이 실행되지 않도록 해당 테이블에 배타적 lock(exclusive lock)을 얻습니다.

> 2. 동시성 제어 (Concurrency Control)

동시성 제어란 DBMS가 다수의 사용자 사이에서 동시에 작동하는 트랜잭션에서 DB를 보호하는 것을 의미합니다. 한 마디로 동일한 테이블, 테이블의 데이터에 동시에 쓰기, 수정, 삭제 등으로부터 보호하는 의미입니다. 동시성을 허용하면 일관성이 낮아지게 됩니다. 동시성을 제어하기 위해 Lock과 SET TRANSACTION 명령어를 사용해 트랜잭션의 격리성 수준(transaction isolation level)을 조절할 수 있는 기능을 제공합니다. 또한 동시성을 제어하는 방법으로 비관적 동시성 제어(Pessimistic Concurrency Control)와 낙관적 동시성 제어

> 3. 비관적 동시성 제어 (Pessimistic Concurrency Control)

사용자들이 같은 데이터를 동시에 수정할 것이라고 가정을 하고 데이터를 읽는 시점에 lock을 걸고 트랜잭션이 완료가 될 때까지 lock 상태를 유지합니다. 즉, select 시점에 lock을 걸기 때문에 데이터의 일관성을 확실히 지킬 순 있지만 시스템의 동시성을 저하시킬 수 있습니다. 따라서 옵션으로 nowait(lock에 즉시 충돌되면 exception 처리) 이나 wait(지정한 초만큼 대기)를 주어야 합니다. nowait이나 wait에 너무 긴 시간을 주게 된다면 대기 상태가 엄청나게 길어져 기아 상태(starvation)에 빠질 수 있으므로 적절한 시간을 주어야 합니다.

> 4. 낙관적 동시성 제어 (Optimistic Concurrency Control)

사용자들이 같은 데이터를 동시에 수정하지 않을 것이라고 가정을 합니다. 비관적 동시성 제어와 다르게 데이터를 읽는 시점에 lock을 걸지 않기 때문에 수정 시점에 값이 변경 됐는지 검사해야 합니다. 보통 낙관적 동시성 제어를 구현할 때, version과 같은 플래그성 컬럼을 사용합니다. 수정이 일어날 때마다 version을 1씩 올리기 때문에 조회한 시점의 version과 수정하려할 때 version이 다르면 수정을 진행하지 않습니다.

lock의 문제점은 read와 write가 서로 간섭을 하기 때문에 동시성 문제+가 발생하고 데이터 일관성에 문제가 생기는 경우가 있어 lock을 더 오래 유지하거나 테이블 단위의 lock을 사용하기도 합니다. 이럴 경우 동시성이 저하됩니다. 이러한 문제를 해결하기 위해 MVCC가 탄생했습니다.

> 5. 다중 버전 동시성 제어 (Multi-Version Concurrency Control, MVCC)

MVCC는 동시 접근을 허용하는 데이터베이스에서 동시성을 제어하기 위해 사용하는 방법 중 하나입니다. MVCC 모델에서 데이터에 접근하는 사용자는 해당 시점에 데이터베이스의 snapshot을 읽습니다. 이 snapshot 데이터에 대한 변경이 완료될 때 (또는 트랜잭션이 완료될 때)까지 만들어진 변경사항은 다른 데이터베이스의 사용자가 볼 수 없습니다. 사용자가 데이터를 업데이트 하면 이전의 데이터를 덮어 씌우는 것이 아니라 새로운 버전의 데이터를 이전 버전의 데이터와 비교해서 변경된 내용을 UNDO에 생성합니다. 이러한 방식으로 하나의 데이터에 대해 여러 버전의 데이터가 존재하게 되고 사용자는 마지막 버전의 데이터를 읽게 됩니다.
        
이러한 방식은 lock을 사용하지 않기 때문에 RDBMS보다 빠르게 동작합니다. 또한 데이터를 읽을 때, 다른 사용자가 해당 데이터를 삭제, 수정해도 영향을 받지 않습니다. 데이터는 여러 버전으로 존재하기 때문에 주기적으로 데이터를 정리해야 합니다. MVCC 모델은 하나의 데이터에 대한 여러 버전의 데이터를 허용하기 때문에 데이터 버전이 충돌될 수 있으므로 애플리케이션 영역에서 이러한 문제를 해결해야 합니다. 또한 UNDO 블록 I/O, CR Copy 생성, CR 블록 캐싱 같은 부가적인 작업의 오버헤드가 발생합니다.
#### MVCC란 하나의 logical object에 대해서 여러개의 physical한 version이 존재하는 기법.

> 관련링크

[MVCC에 관하여 정리](https://this1.tistory.com/entry/MVCC%EC%97%90-%EA%B4%80%ED%95%98%EC%97%AC-%EC%A0%95%EB%A6%AC)

[17. MVCC](https://mysqldba.tistory.com/335)

[MVCC 동시성 제어](https://scorpio-mercury.tistory.com/32)

> 격리성에 관한 자료
> 
[Transaction과 Isolation Level](https://scorpio-mercury.tistory.com/33?category=388494)


## PostgreSQL의 MVCC 작동방식

## 데이터베이스 내에 다중 버전의 데이터를 저장(MGA)

PostgreSQL, Interbase, SQL Server에서 사용하는 방식이다.

데이터베이스 내에 다중 버전의 데이터를 저장하다가, 더 이상 필요하지 않을 때 데이터를 정리한다.

데이터베이스 내에 다중 버전의 데이터가 저장되기 때문에, 파일 사이즈가 증가한다.

![https://media.vlpt.us/images/yangsijun528/post/4bfd81a2-2e77-463e-b549-34bf6baaa68f/image.png](https://media.vlpt.us/images/yangsijun528/post/4bfd81a2-2e77-463e-b549-34bf6baaa68f/image.png)

너무 많은 버전이 생기면 부하가 올 수 있기 때문에 Vacuum을 해줘야한다.


