---
title: "DB Postgres json, array 관련 함수"
date: 2021-02-21
comments: true
read_time: false
categories:
  - DB
tags:
  - postgres
---

이제껏 간단한 sql만 작성하다보니 배열로 조회하거나 하는 등 일이 없었으나 이번에 필요한 상황이 생겨 서칭을 하다 상당히 많은 함수들에 대해 알게 되었다.

## row_to_json

출력되는 row에 대해 column명을 키값으로 하여 `json`형태로 반환 시켜 주는 함수

```sql
select row_to_json(tmp) from tmp;
select row_to_json(row(col1, col2)) from tmp;
```

## array_agg

`group by`와 함께 쓰이며 같이 묶이게 되는 값들을 `array`로 반환 시켜 준다.

```sql
select
  col1
  , array_agg(col2) as col_agg
from
  test_table
group by
  1
```

## string_agg

`group by`와 함께 쓰이며 같이 묶이게 되는 값들을 구분자를 지정하여 `string`으로 반환 시켜 준다.

```sql
select
  col1
  , string_agg(col1::text, ', ') as col_agg
from
  test_table
group by
  1
```

## array_to_string

`array` 값을 특정한 구분자를 지정하여 구분자 이어 붙여 `string` 타입으로 변환 시켜주는 함수

```sql
with tmp as (
  select
    col1
    , array_agg(col1) as col2
  from
    test_table
)
select
  array_to_string(col2, ', ') as col_string
from
  tmp
```

## string_to_array

`array_to_string`의 반대로 `string` 타입을 특정 구분자로 구분하여 `array`로 만들어 준다.

```sql
select
  string_to_array(col2, ', ') as col_string
from
  tmp
```
