---
title: "Postgres - coalesce, with as"
date: 2021-04-01
comments: true
read_time: false
categories:
  - SQL
tags:
  - postgres
---

알아두면 생각보다 많이 쓰고 유용한 SQL 정리

## coalesce 함수

`null`인 값을 무시하고 `null`이 아닌 인자를 출력해주는 함수이다.  
case 문을 써서도 할 수 있는 부분이지만 `coalesce`함수를 사용하는게 보기도 좋고 성능도 좋다.

```sql
select coalesce(tt.a, tt.b, tt.c, 'test') as tests
  , tt.d
from test_table tt
```

전달받은 인자 중 `null`이 아닌 값이 있을 때까지 인자 순서대로 확인한다.  
위 코드에서는 모든 값이 `null`일지라도 `test`를 출력하게 될 꺼다. 이처럼 기본값도 쉽게 설정이 가능하다.

## with as 문

임시 테이블을 만들어 사용할 때 사용하는 구문이다.  
가독성과 재사용성이 좋고, 계층형 쿼리를 구현할 수 있게 해준다.

```sql
with temp_table as (
  select
    c1
    , c2
    , c3
    , count(c4)
  from table_1
  group by 1, 2, 3
)
select
  *
from table_2 t2
inner join temp_table tt on tt.c1 = t2.c1
```
