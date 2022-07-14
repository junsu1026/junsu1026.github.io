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

1. 명시적 LOCK (Explicit Lock)
PostgreSQL은 여러 lock 모드들을 사용하여 테이블의 데이터를 동시 접근을 제어합니다. 이와 같은 lock 모드는 다중 버전 동시성 제어(MVCC)를 제공하지 않는 상황에서 어플리케이션을 제어하는 lock을 쓸 수 있고 대부분의 PostgreSQL 명령들은 해당 명령이 실행되는 동안 명령 실행의 대상이 되는 테이블이 삭제되거나 수정되지 않도록 적절한 모드의 lock을 자동으로 얻습니다. 예를 들어, 특정 테이블에서 truncate가 실행되는 동안 다른 작업이 실행되지 않도록 해당 테이블에 배타적 lock(exclusive lock)을 얻습니다.

2. 동시성 제어 (Concurrency Control)
동시성 제어란 DBMS가 다수의 사용자 사이에서 동시에 작동하는 트랜잭션에서 DB를 보호하는 것을 의미합니다. 한 마디로 동일한 테이블, 테이블의 데이터에 동시에 쓰기, 수정, 삭제 등으로부터 보호하는 의미입니다. 동시성을 허용하면 일관성이 낮아지게 됩니다. 동시성을 제어하기 위해 Lock과 SET TRANSACTION 명령어를 사용해 트랜잭션의 격리성 수준(transaction isolation level)을 조절할 수 있는 기능을 제공합니다. 또한 동시성을 제어하는 방법으로 비관적 동시성 제어(Pessimistic Concurrency Control)와 낙관적 동시성 제어
