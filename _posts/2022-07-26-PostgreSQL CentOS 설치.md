---
layout: post
title:  "PostgreSQL CentOS 설치"
date:   2022-07-25T14:25:52-05:00
author: Junsu Noh
categories: PostgreSQL
tags:	jekyll welcome
cover:  "/assets/instacode.png"
---

• dnf = Dandified Yum으로 Yum의 차세대 버전

Linux에 PostgreSQL을 설치할 때 yum을 사용해도 되지만 dnf를 사용하는 것이 최신 방법이다.

[Linux에서 RPM 패키지 관리를 위한 DNF 명령](https://dejavuhyo.github.io/posts/dnf-commands-for-rpm-package-management/)

1. 패키지 설치

```bash
# PostgreSQL 다운로드 RPM 저장소 설치
sudo dnf(yum도 사용가능) install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-8-x86_64/pgdg-redhat-repo-latest.noarch.rpm

# (옵션)내장된 PostgreSQL 비활성화
sudo dnf -qy module disable postgresql

# PostgreSQL 설치
sudo dnf install -y postgresql13-server
```

![linux dnf postgre 1](https://user-images.githubusercontent.com/90672736/180904614-f2e1167e-9e94-4135-b425-8720be6e27f8.png)



1. 초기화 및 서비스 등록

```bash
# PostgreSQL 초기화
sudo /usr/pgsql-13/bin/postgresql-13-setup initdb

# 서비스 등록
sudo systemctl enable postgresql-13
 
# 서비스 실행
sudo systemctl start postgresql-13
 
# 서비스 동작 확인
sudo systemctl status postgresql-13
```
![linux dnf postgre 2](https://user-images.githubusercontent.com/90672736/180904785-c94765b4-45ad-49e0-89f0-b22a1f4004b1.png)

1. 접속확인

```bash
# 전용 계정으로 전환
su - postgres
 
# psql 실행
psql
 
# 초기 DB 확인
\l

# 나가기(종료)
\q

# bash 나가기
exit

# 초기 비밀번호 설정
su - postgres
psql 

ALTER USER postgres with encrypted password '<패스워드>';

# 버전 확인
select version();
```
![linux dnf postgre 3](https://user-images.githubusercontent.com/90672736/180904835-ad1799dd-0664-4101-a13f-8e92b4752e31.png)


```sql
- DB 접속
# psql -U [DB사용자계정] [데이터베이스명]

- Postgre SQL shell 진입시
# psql [스키마명]

- DB 데이터베이스 출력
# \l or \list

 
- DB 데이터베이스 선택
# \c [데이터베이스 명]

 
- DB 데이터베이스 생성
#CREATE DATABASE dbname OWNER ownnerName

- DB 테이블 출력
# \dt

 
- 테이블 구조 조회 : 오라클의 DESCRIBE TABLE
  # \d+ 테이블명
  # \d+ 테이블명

 
- DB 해당 테이블 정보 출력 
# \d [테이블명] 

 
- DB 사용자 권한 정보
# \du

 
- DB 출력 변경 (unpivot)
# \x

 

- DB 쿼리 결과값 파일 저장(쿼리 결과값을 출력하지 않고 파일에 저장됨)
# \o [파일경로]

# [select 문 명령어]

 
- DB 실행중인 쿼리 조회
# SELECT * FROM pg_stat_activity ORDER BY query_start ASC;

- SQL문 파일을 실행
  $ psql -U [사용자명] [DB명] < [SQL파일명]

- 파일로부터 DB로 dump, 또는 DB로부터 파일로 dump
  # COPY 테이블명 [(컬럼명1, 컬럼명2,...)] FROM '파일명'
  # COPY 테이블명 [(컬럼명1, 컬럼명2,...)] TO '파일명'
  구분자를 지정하지 않으면 기본적으로 탭으로 지정됨. 전체 옵션은 아래와 같음.
  http://www.postgresql.org/docs/9.2/static/sql-copy.html 참고

 

- 파일로 dump

   pg_dump -d [db명]  -U [user] -F t > [파일명.tar]

-d, --dbname : Backup할 Database 명.
-h, --host : Database 주소.
-U, --username : Database 접속 시 User ID
-F, --format : Backup Format. 필자는 주로 tar 파일로 backup하기 때문에 't'를 사용한다.
-f, --file : Backup File Name
-t, --table : 특정 Table만 Backup하려할 때 대상이 되는 Table 명
-j, --jobs : Backup 시 병렬 처리 여부와 그 정도.
-v, --verbose : 진행 과정 표시.
- dump to restore

  pg_restore -h localhost -U postgres -C -d postgres -F t [dumpFile]

-d, --dbname : Restore하는 Database 명.
-h, --host : Database 주소.
-U, --username : Database 접속 시 User ID
-F, --format : Restore File의 Format.
-t, --table : 특정 Table만 Restore하려할 때 대상이 되는 Table 명
-j, --jobs : Restore 시 병렬 처리 여부와 그 정도.
-v, --verbose : 진행 과정 표시
-C, --create : Target DB를 새로 만들면서 Restoration 진행.
-c, --clean : Restoration 시에 같은 이름의 Database Object가 발견되면 Drop 후에 Create하게 함.
-O, --no-owner : 원본 DB의 Owner가 복구할 위치에 존재하지 않을 경우 복구 시 다량의 에러가 발생한다. 이를 막기 위해  DB 복구시 OWNER를 명시하지 않고 진행하게 함.
```

SQL 기본 명령어

```sql
# 테이블 생성
Create table <테이블명>(<컬럼명1> 데이터타입1, <컬럼명2> 데이터타입2 ....  );

# 테이블 검색
Select <컬럼명> from <테이블명>;

# 데이터 삽입
Insert into <테이블명>[(컬럼명)] values(값1,값2....);

# 데이터 수정
Update <테이블명> set <컬럼명> = '<바꿀값>'[where <컬럼명> = '<값>'];

# 데이터 삭제 
Delete from <테이블명> where <컬럼명> = '<값>';

```

리눅스 postgresql 관리 명령어

[리눅스 / 명령어 / systemctl](https://www.manualfactory.net/10507)

## 참고 링크

기본적인 명령어

[[PostgreSQL] 기본 명령어](https://rianshin.tistory.com/68)

[우분투 PostgreSQL 설치](http://createlee75.blogspot.com/2016/12/postgresql.html)

[[PostgreSQL] CentOS 7에서 PostgreSQL 설치 및 시작](https://hgko1207.github.io/2020/09/10/postgresql-1/)

[[CentOS] PostgreSQL 설치 가이드](https://brunch.co.kr/@daniellim/38)

[PostgreSQL 13 Linux 설치하기](https://confluence.curvc.com/pages/releaseview.action?pageId=98042872)
