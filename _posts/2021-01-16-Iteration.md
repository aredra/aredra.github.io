---
title: "JS Iteration"
date: 2021-01-16
comments: true
read_time: false
toc: true
categories:
  - JS
tags:
  - Iteration
---

Iteration의 사전적 의미는 *반복*으로 의미 그대로이나 일반적인 반복문 `for`, `while` 등의 반복과는 개념의 차이가 있다.

```js
const list = [1, 2];

for (let value of list) {
  console.log(value);
}

const obj = list[Symbol.iterator]();
console.log(obj.next());
console.log(obj.next());
console.log(obj.next());
```

## Iteration 프로토콜

Iteration를 하기 위해서는 프로토콜이 필요하며, 이는 구문 또는 빌트인이 아니다.

- 오브젝트가 반복할 수 있는 구조
- Iteration 함수를 가지고 있어야 함
- Iteration Protocol 구분
  - Iterable Protocol
  - Iterator Protocol
- 상기 Protocol를 맞춰 개발할 경우 Iteration 가능

## 빌트인 이터러블 오브젝트

기본값으로 이터러블 프로토콜을 가지고 있는 오브젝트(Symbol.iterator()를 지님)

> Array, Argument, String, TypedArray, Map, Set, DOM NodeList

## Iterator Object

- `Symbol.iterator();`를 호출하면 반환되는 오브젝트
- 이터레이터 오브젝트는 `next()`가 있어야 함
- 해당 오브젝트를 이터레이터(Iterator)라고도 지칭함
- 결과 값은 `{value: any, done: boolean}` 형태
  - value: 해당하는 순번의 값 - 끝났을 경우 `undefined`
  - done: 끝인지 아닌지 확인하는 값
