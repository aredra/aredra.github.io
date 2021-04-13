---
title: "JS - Generator 간략 정리"
date: 2021-04-13
comments: true
read_time: false
categories:
  - JS
tags:
  - generator
---

일반적인 함수는 값을 하나만 반환한다. 하지만 여러 값을 반환 할 수 있는 함수가 `Generator` 함수이다.

## Generator

기본적으로 함수의 형태를 지니고 있고, 함수와 유사하게 호출 할 수 있고 생성하는 방법은 아래와 같다.

```js
// 선언
function* f1(param) {}

// 표현
const f2 = function* (param) {};

// 생성자 이용
const f3 = Object.getPrototypeOf(function* () {}).constructor;
const f4 = new f3("param", "");
```

## yield

`Generator` 함수의 핵심이라고 생각하며, 개인적으로 중간지점 이라는 생각이 든다.  
`next()` 함수가 실행 될 때마다 하나씩 위에서 진행된 코드와 오른쪽 표현식이 실행 된 값을 `{value, done}` 형태로 반환한다.

```js
const f = function* (param) {
  const foo = yield;
  const bar = yield 1 + param;
  yield;
  yield foo + bar;
  return yield;
};
const f1 = f(10);

console.log(f1.next(1)); // {value: undefined, done: false}
console.log(f1.next(2)); // {value: 11, done: false}
console.log(f1.next(3)); // {value: undefined, done: false}
console.log(f1.next(4)); // {value: 5, done: false}
console.log(f1.next(5)); // {value: undefined, done: false}
console.log(f1.next(6)); // {value: 6, done: true}
```

`next()`가 호출 될 때마다 순서대로 `yield`를 찾아가 값을 반환하며, 파라미터의 경우 반환 `yield`위치에 들어간다.  
`yield`가 더 이상 없을 경우 `{value: undefined, done: true}`를 반복하며 `return` 있다면 마지막 이후 값을 반환해준다.

## 유용한 methods

- `return()` : 종료
- `throw()` : Generator 내부에 오류 발생
