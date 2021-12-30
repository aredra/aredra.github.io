---
title: "JS - iterable 친해지기"
date: 2021-12-30
comments: true
read_time: false
toc: true
categories:
  - JS
tags:
  - iterable
  - iterator
---

전개연산자, 제너레이터.. 기존에 알던 개념이지만 이터러블의 관점에서는 생각해본적이 없는거 같아 정리하는 메모.

## 이터러블/이터레이터 프로토콜

이터러블을 for of, 전개연산자 등에 동작하도록 하는 규약

- 이터러블: 이터레이터를 리턴하는 [Symbol.iterator]() 속성을 가진 객체
- 이터레이터: {value, done} 형태의 객체를 리턴하는 next() 메서드

## Well-formed 이터러블

이터레이터가 자기 자신을 반환하는 [Symbol.iterator]를 가진 경우

```js
const iterableSample = {
  [Symbol.iterator]() {
    let i = 5;
    return {
      next() {
        return i === 0 ? { done: true } : { value: i--, done: false };
      },
      [Symbol.iterator]() {
        return this;
      },
    };
  },
};

const iterator = iterableSample[Symbol.iterator]();
const iterator2 = iterableSample[Symbol.iterator]();
console.log(iterator.next()); // { value: 5, done: false }
console.log(iterator2.next()); // { value: 5, done: false }
for (const value of iterableSample) {
  console.log(value); // 5 4 3 2 1
}
console.log(iterator.next()); // { value: 5, done: false }
console.log(iterator.next()); // { value: 4, done: false }
console.log(iterator.next()); // { value: 3, done: false }
console.log(iterator.next()); // { value: 2, done: false }
console.log(iterator.next()); // { value: 1, done: false }
console.log(iterator.next()); // { done: true }
```

## 전개연산자

```js
let arr1 = [1, 2, 3, 4, 5];
let tempIter = arr1[Symbol.iterator]();
tempIter.next(); // { value: 1, done: false }
for (let i of arr1) {
  console.log(i); // 1, 2, 3, 4, 5
}
console.log(...arr1); // 1 2 3 4 5
arr1[Symbol.iterator] = null;
console.log(...arr1); // error
```

## 제너레이터

이터레이터이자 이터러블을 생성하는 함수, 🧨 **순회하는 값을 만들 수가 있다.**

```js
function* gen() {
  yield 1;
  yield 2;
  yield 3;
  return 99;
}
let iterator = gen();
console.log(iterator[Symbol.iterator] === iterator); // false
console.log(iterator.next()); // { value: 1, done: false }
console.log(iterator.next()); // { value: 2, done: false }
console.log(gen().next()); // { value: 1, done: false }

for (const item of gen()) {
  console.log(item); // 1 2 3
}
```

## map, filter, reduce 샘플코드

Array의 내장 메소드이지만 아래와 같이 작성할 수 있고 이럴 경우 다형성이 커진다.

```js
// map
const map = (iter, f) => {
  let res = [];
  for (const item of iter) {
    res.push(f(item));
  }
  return res;
};

console.log(map(document.querySelectorAll("*"), (el) => el.nodeName));
console.log(document.querySelectorAll("*").map((el) => el.nodeNAme)); // 오류

// filter
const filter = (iter, f) => {
  let res = [];
  for (const item of iter) {
    if (f(item)) {
      res.push(item);
    }
  }
  return res;
};

console.log(
  filter(document.querySelectorAll("*"), (el) => el.nodeName === "DIV")
);

// reduce
const reduce = (iter, f, init) => {
  let res = init || iter[Symbol.iterator]().next().value;
  for (const item of iter) {
    res = f(res, item);
  }
  return res;
};

console.log(reduce([1, 2, 3], (a, b) => a + b, 0));
```

<hr/>

_개인이 참고하고자 작성한 글이며, 잘못된 정보가 있을 수 있습니다. 잘못된 정보는 메일로 보내주시면 감사하겠습니다._ 🙏
