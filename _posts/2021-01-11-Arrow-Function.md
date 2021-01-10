---
title: "Arrow Function"
date: 2021-01-11
comments: true
read_time: false
toc: true
categories:
  - JS
tags:
  - ES6
  - arrow-function
---

<br>
Arrow-function도 나온지 오래 됐지만 다시 정리를 해보려 한다.

## 특징

아래와 같은 특징들을 지고 있다.

1. prototype과 constructor가 없음
2. prototype을 쓸 수가 없고 확장도 안됨
3. arguments도 못 씀 -> rest parameter로 사용하여야 함
4. 없는게 있으므로 그만큼 가벼움
5. new 연산자로 instance 생성 불가
6. 화살표 함수는 함수에 this를 가지지 않음 -> 스코프의 this를 사용함
7. 바인딩 된 this 참조가 바뀌지 않는다.

## 사용예제

사용에 있어서 생략이 가능 한게 많아서 조금 헷갈릴 수 있다.

1. 파라미터 1개일 경우 () 생략가능
2. 실행문이 1행일 경우 return 생략가능
3. 파라미터가 불필요할 경우 ()만 해서 작성

```js
// 기본형
const add = (a, b) => {
  return a + b;
};

const log = () => console.log("arrow-function");
```
