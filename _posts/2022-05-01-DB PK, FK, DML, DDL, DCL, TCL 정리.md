---
layout: post
title:  "PK, FK, DML, DDL, DCL, TCL 정리"
date:   2022-04-30T14:12:52-05:00
author: Junsu Noh
categories: DB
tags:	jekyll welcome
cover:  "/assets/instacode.png" 
---

## PK, FK, DML, DDL, DCL, TCL 정리


관계형 데이터베이스이며 각 테이블들이 연관관계를 가지면서 데이터들을 다루게 된다.
관계형 데이터 베이스에서 가장 중요한 PK와 FK 4줄정리!!

Primary Key : 
              1. 중복 값을 가질 수 없다.
              2. 한번 값이 지정되면 변경할 수 없다.
              3. 각 테이블당 1개만 사용 가능하다.
              4. 각 행을 고유하게 식별 가능하게 해준다.
Foreign Key :
              1. PK의 자식 테이블에 생성한다.
              2. 참조하는 테이블의 PK값 또는 UK값과 일치하여야 한다 그렇지 않으면 null로 지정되어야 한다.
              3. PK와 데이터 타입이 일치하여야 한다.
              4. 참조하는 PK에 존재하는 값만 사용이 가능하다.
              
데이터베이스 용어 정리 : 
1. DML(Data Manipulation Language) : 
        - SELECT 
        - INSERT
        - UPDATE
        - DELETE
        - MERGE
2. DDL(Data Definition Language) :
        - CREATE
        - ALTER
        - DROP
        - RENAME
        - TRUNCATE
        - COMMENT
3. DCL(Data Control Language) : 
        - GRANT
        - REVOKE
4. TCL(Transaction Control Language) :
        - COMMIT
        - ROLLBACK
        - SAVEPOINT
