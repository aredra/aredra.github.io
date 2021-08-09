---
title: "Postgresql - collate"
date: 2021-08-02
comments: true
read_time: false
toc: true
categories:
  - Diary
  - DB
  - Postgresql
tags:
  - collate
---

업무 중 정렬이 이상하다는 이슈가 들어왔다. 보아하니 쿼리로 정렬(order by)하여 조회 중이였다. 문제가 없어 보였다.

## 원인

한글 정렬이 문제였다. `DB`를 처음 만들 때 `LO_COLLATE` 를 설정할 수 있는데 값이 `en_US.uft8` 로 들어가 있었다.  
아래와 같은 쿼리로 현재 `DB` 들의 설정값을 확인할 수 있다.

```sql
select datname, datcollate from pg_database;
```

## 해결책

1. `order by` 에 `collate` 작성하여 조회하기
2. `BE`단에서 코드로 정렬하여 전달하기
3. `DB`를 `dump` 해서 새로 만들기

개발 중인 DB 라면 3번을 추천. 새로 만들 때 아래와 같이 설정하자.

```sql
create database [database_name] LC_COLLATE 'C';
```

1번의 경우 그냥 `order by` 뒤에 작성하면 된다. 어렵지 않다.

```sql
select tt.*
from test_table tt
order by tt.user_nickname collate "C";
```

## 기타

구글링 중 `LO_COLLATE` 값을 `C` 로 하라고 `ko_KR.uft8` 은 쓰지 말라고하는 글을 보았었는데 음 일단 필자는 잘된다. 정확한 사유는 아직 모르겠다. 😵
