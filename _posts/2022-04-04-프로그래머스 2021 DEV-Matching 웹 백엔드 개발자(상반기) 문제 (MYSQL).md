---
layout: post
title:  "프로그래머스 2021 DEV-Matching: 웹 백엔드 개발자(상반기) 문제"
date:   2022-04-03T14:12:52-05:00
author: Junsu Noh
categories: 프로그래머스
tags:	jekyll welcome
cover:  "/assets/instacode.png" 
---

## 프로그래머스 2021 DEV-Matching: 웹 백엔드 개발자(상반기) 문제 



##### 문제

이 서비스에서는 공간을 둘 이상 등록한 사람을 "헤비 유저"라고 부릅니다. 헤비 유저가 등록한 공간의 정보를 아이디 순으로 조회하는 SQL문을 작성해주세요.



SubQuery와 having의 조건을 사용해서 푸는 문제다.



![프로그래머스 2021 DEV-Matching 웹 백엔드 개발 문제](https://raw.githubusercontent.com/junsu1026/junsu1026.github.io/images/assets/img/프로그래머스 2021 DEV-Matching 웹 백엔드 개발 문제.PNG)



서브쿼리 안에서 HOST_ID의 갯수가 2개이상인 것들을 select 해주고 HOST_ID의 값들을 뽑아내면 된다.



```
SELECT * from PLACES 
where HOST_ID in 
(select HOST_ID 
from PLACES 
group by 
HOST_ID 
having count(HOST_ID) > 1)
```

