---
layout: post
title:  "JAVA Collection"
date:   2022-03-20T14:12:52-05:00
author: Junsu Noh
categories: JAVA
tags:	jekyll welcome
cover:  "/assets/instacode.png" 
---

## JAVA Collection



Collection에는 크게 List, Set, Map이 존재한다.  이 셋을 구분하는 가장 큰 이유는 "순서", "데이터중복여부" 다.

List : 순서 O, 데이터 중복 O

Set : 순서 X, 데이터 중복 X

Map : Key&Value 저장, Key 중복 X, Value 중복 O



List는 인덱스를 사용하기 때문에 내용이 중복되어도 상관이 없다

Array List와 LinkedList의 차이는 그냥 Array는 검색이 빠르고 추가 및 삭제가 LinkedList보다 빠르다 하지만 

LinkedList는 중간에 추가,제거 작업이 빠르다. 하지만 앞뒤의 인덱스 값을 참조하기 때문에 메모리를 많이 차지 하게 된다.



Set은 인덱스를 사용하지 않기 때문에 중복을 허용하지 않는다.

기본적으로 많이 사용하는 HashSet이 있다.



Map은 Key와Value를 사용하기 때문에 Key는 중복되어선 안되지만 Value는 중복이 되어도 상관이 없다



