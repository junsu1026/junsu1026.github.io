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
