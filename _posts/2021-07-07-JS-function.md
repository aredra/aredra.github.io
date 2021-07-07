---
title: "JS - fucntion"
date: 2021-07-05
comments: true
read_time: false
toc: true
categories:
  - JS
tags:
  - function
  - property
---

객체에는 프로퍼티가 있다. JS 에서 함수는 객체인데 그럼 프로퍼티는?  이에 대한 간략 메모

## name, length

함수에서 먼저 기본적으로 사용할 수 있는 프로퍼티 2가지가 있다.
name 과 length 이다. name 은 함수의 이름을, length 는 파라미터의 갯수를 확인할 수 있다.

```js
function muYaho(a, b, ...c) {
  return a + b;
}

const test = function() {
  return 'test';
};

console.log(muYaho.name);
console.log(muYaho.length);
console.log(test.name);
console.log(test.length);
```
위의 예제에서 보면 표현식이던 선언식이던 상관은 없으며 rest 파라미터는 포함시키지 않는다.

## Custom Property

커스텀으로 프로퍼티를 사용할 수 있다. 하지만 프로퍼티는 함수 내 변수와는 다르다는 점을 주의해야 한다.

```js
function muYaho() {
  const counter = 0;
  return counter;
}
muYaho.counter = 1;

console.log(muYaho.counter);
console.log(muYaho());
```

## etc

함수 선언문은 즉시 실행할 수 없다.

```js
// 불가능, IIFE 를 사용해야한다.
function test() {
  alert('test');
}()
```
기명 함수를 통해서 스스로를 호출할 수 있다.

```js
let sayHi = function func(who) {
  if (who) {
    alert(`Hello, ${who}`);
  } else {
    func("Guest"); // func를 사용해서 자신을 호출합니다.
  }
};
```

## 참조 
[모던 자바스크립트 - 객체로서의 함수](https://ko.javascript.info/function-object)