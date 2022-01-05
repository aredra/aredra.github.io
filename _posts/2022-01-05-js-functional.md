---
title: "JS - Functional programming 기초"
date: 2022-01-05
comments: true
read_time: false
toc: true
categories:
  - JS
tags:
  - functional
---

(임인년🐯 새해 첫 글이다. 회고도 조만간 해야지...)

함수형 프로그래밍 관련 기초

함수형 프로그래밍은 프로그래밍에 대한 패러다임으로 필수 사항이 아니다. 현재 웹개발에서 대세(주관적) 선언형 프로그래밍 패러다임을 따르고 있다.

> [위키백과 - 함수형 프로그래밍](https://ko.wikipedia.org/wiki/%ED%95%A8%EC%88%98%ED%98%95_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D#cite_note-expression_style-1)

## 함수 활용

함수형 프로그래밍 이라는 명칭에 맞게 함수가 중요하다. 함수를 값으로 생각하며 값을 다루는 함수처럼 함수를 다루는 함수를 다루어야 한다.

간단하게는 아래와 같은 함수를 작성하여 사용할 수 있다. 명칭은 정해진 것은 아니므로 `composition` 등 다르게 지어도 무방하다.

```js
// curry 받아둔 함수를 원하는 시점에 평가를 시키는 함수 - 함수를 받아 함수를 반환, 원하는 만큼 인자를 받아 받아둔 함수를 실행하는 함수
const curry =
  (f) =>
  (a, ..._) =>
    _.length ? f(a, ..._) : (..._) => f(..._, a);

// go 인자 순서대로 실행되며 실행된 값을 다음 함수로 넘겨주는 함수 - 즉시 평가
const go = (...args) => reduce(args, (acc, f) => f(acc));

// pipe 함수를 인자를 받아 합성된 함수를 만드는 함수
const pipe =
  (f, ...fns) =>
  (...args) =>
    go(f(...args), ...fns);
```

## 사용 예제 1

전에 작성했던 코드를 긁어다가 위 함수를 다루는 함수에 추가하여 아래와 같은 코드를 작성했을 때

```js
const people = [
  {
    name: "John",
    age: 30,
    income: 23000,
  },
  {
    name: "Peter",
    age: 25,
    income: 15000,
  },
  {
    name: "Amy",
    age: 27,
    income: 8000,
  },
  {
    name: "Hannah",
    age: 35,
    income: 30000,
  },
  {
    name: "Michael",
    age: 29,
    income: 6000,
  },
];

// map
const map = curry((iter, f) => {
  let res = [];
  for (const item of iter) {
    res.push(f(item));
  }
  return res;
});

// filter
const filter = curry((iter, f) => {
  let res = [];
  for (const item of iter) {
    if (f(item)) {
      res.push(item);
    }
  }
  return res;
});

const reduce = curry((iter, f, init) => {
  const iterable = iter[Symbol.iterator]();
  let res = init || iterable.next().value;
  for (const item of iterable) {
    res = f(res, item);
  }
  return res;
});

// 같은 값을 표현하는 여러 방법
console.log(
  reduce(
    map(
      filter(people, (p) => p.age < 30),
      (p) => p.age
    ),
    (a, b) => a + b
  )
);
```

일 때 위의 `console.log` 는 보면 계산을 안에서부터 해서 밖으로 나와야 하며 직관적이다 라고 하기에는 어려운 것 같지만 `go` 함수를 이용하여 아래처럼 순서대로 작성이 가능하도록 직관적인 변형이 가능하다.

```js
// go 함수 이용
go(
  people,
  (people) => filter(people, (p) => p.age < 30),
  (people) => map(people, (p) => p.age),
  (ages) => reduce(ages, (a, b) => a + b)
);
```

여기서 `curry` 함수까지 활용을 한다면 아래처럼 좀 더 깔끔하고 직관적인 코드를 작성할 수 있다.

```js
// go, curry 함수 이용
go(
  people,
  filter((p) => p.age < 30),
  map((p) => p.age),
  reduce((a, b) => a + b)
);
```

## 사용 예제 2

아래의 코드를 보면 `add` 함수가 `totalIncome`, `totalAge` 에서 사용됨을 알 수 있다. `(a, b) => a + b` 해당 함수를 그냥 넣을 수 있으나 공통으로 사용하는 경우 변수에 담아두고 사용할 경우 변경이 용이하며, 함수 내용을 이해하여 동작을 이해하는 것보다 함수명으로 어떤 기능을 하는지 좀 더 직관적으로 알 수 있다.

```js
const add = (a, b) => a + b;

const sum = (iter, f) => go(iter, map(f), reduce(add));
const sumCurry = curry(sum);

const totalIncome = pipe(
  map((p) => p.income),
  reduce(add)
);
const totalIncome2 = (people) => sum(people, (p) => p.income);
const totalIncome3 = sumCurry((p) => p.income);

console.log("totalIncome", totalIncome(people));
console.log("totalIncome2", totalIncome2(people));
console.log("totalIncome3", totalIncome3(people));

const totalAge = pipe(
  map((p) => p.age),
  reduce(add)
);
const totalAge2 = (people) => sum(people, (p) => p.age);
const totalAge3 = sumCurry((p) => p.age);

console.log("totalAge", totalAge(people));
console.log("totalAge2", totalAge2(people));
console.log("totalAge3", totalAge3(people));
```

아래의 totalIncome 의 경우는 income이 있는 객체에 대해서만 동작을 할 수 있다. 이 부분을 sum 함수처럼 다시 함수를 받아서 좀 더 추상화 할 수 있다.

```js
const sum = (iter, f) => go(iter, map(f), reduce(add));

// const totalIncome = pipe(
//   map((p) => p.income),
//   reduce(add)
// );
const totalIncome = (people) => sum(people, (p) => p.income);
```

좀 더 간결하게 작성을 하고자 한다면 `curry` 함수를 통해 아래처럼 수정할 수 있다.

```js
const sumCurry = curry(sum);

// const totalIncome = (people) => sum(people, (p) => p.income);
const totalIncome = sumCurry((p) => p.income);
```

## 참조

- [인프런 - 함수형 프로그래밍](https://www.inflearn.com/course/functional-es6)
- [위키백과 - 함수형 프로그래밍](https://ko.wikipedia.org/wiki/%ED%95%A8%EC%88%98%ED%98%95_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D#cite_note-expression_style-1)
<hr/>

_개인이 참고하고자 작성한 글이며, 잘못된 정보가 있을 수 있습니다. 잘못된 정보는 메일로 보내주시면 감사하겠습니다._ 🙏
