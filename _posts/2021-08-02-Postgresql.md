---
title: "Postgresql - explain"
date: 2021-08-02
comments: true
read_time: false
toc: true
categories:
  - DB
  - Postgresql
tags:
  - explain
---

업무 중에 특정 페이지 데이터가 안 나온다는 이슈가 들어왔다. 무언인고 확인 해보니 데이터를 가져오는 속도가 7s 걸리는 걸 확인할 수 있었다. 💦  

## Explain

간단하게 실행하는 쿼리 앞에 EXPLAIN 를 붙이면 실행계획을 볼 수 있다.

```sql
explain 
select 
  tt.*
from test_table tt
```

하지만 나는 출력결과만 보고 문제인 부분을 찾을 수 있는 능력이 없어 해당 사이트를 통해 보고 있다. [explain.dalibo.com](https://explain.dalibo.com/)

> 감사히 쓰고 있습니다!

출력되는 계획 결과를 해당 사이트에 넣어주면 시각화를 해주며 어느 작업과 테이블에서 시간 및 비용이 많이 발생할 예정인지 확인 할 수 있다.

![실행결과](/assets/images/postgresql-explain-stat.png)

explain 명령에는 다양한 옵션이 있는데 아래의 문서를 참조하시면 되겠습니다.  
[Postgresql Explain](https://postgresql.kr/docs/9.6/sql-explain.html)

## 주의사항

필자는 `ANALYZE` 옵션을 자주 사용하는데 해당 옵션은 계획만 보여주는 것이 아님 **직접 실행**된다.  
생성, 갱신, 삭제 등의 작업에는 트랜잭션을 잡으실 필요가 있다.

