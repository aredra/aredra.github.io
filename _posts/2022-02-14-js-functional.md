---
title: "JS - Functional 비동기성1"
date: 2022-02-14
comments: true
read_time: false
toc: true
categories:
  - JS
tags:
  - functional
  - async
---

함수를 합성하다보면 중간에 비동기적이 부분이 있을 수 있다. 이런 비동기 상황을 어떻게 다룰 수 있는지 적어보자.  
모나드와 클레이슬리 합성의 경우는 아직 작성자도 제대로 이해 못 했으며, 직접 찾아보고 이해하시길 부탁드린다.

## Promise

`JS` 에서 비동기적인 이야기를 하면 빼놓기 힘든 `Promise` 다. 아래와 같은 예제에서 `go1` 에서 들어오는 `a` 인자가 `Promise` 인지 확인을 하지 않는다면 `delay100` 을 사용하는 코드는 정상 동작하지 못 할 것이다. `Promise` 의 경우 객체를 반환하기 때문이다.  
그렇지만 `Promise` 를 통해 비동기 상황을 값으로 다룰 수 있게 되며 이를 더 편하게 합성할 수 있게 된다.

```js
const delay100 = (a) =>
  new Promise((resolve) => setTimeout(() => resolve(a), 100));
const go1 = (a, f) => (a instanceof Promise ? a.then(f) : f(a));
const add5 = (a) => a + 5;

const n1 = 10;
const n2 = delay100(10);
go1(go1(n1, add5), console.log);
go1(go1(n2, add5), console.log);
```

## 모나드

모나드에 대해 아직 이론적으로 정립을 못 했다. 찾아보니 딱 이해되는 수준이 아니다... 🥲  
그래도 강의에서 본 내용을 기반으로 적어보자면 일반적으로 함수를 합성하다보면 일반적으로 상정하지 못한 인자가 들어가면서 원하지 않는 결과가 나오는 경우가 발생할 수 있다.  
이런 상황을 회피하고자 함수를 안전하게 합성하는 개념에 대해 모나드 라고 할 수 있는 거 같다.

> 찾아보면 단순히 이런 개념이 아니란 걸 알 수 있을 것이다. 직접 찾아보고 이해하시길 권장드립니다.  
> [데이블팀 테크 블로그 - 모나드와 함수형 아키텍쳐](https://teamdable.github.io/techblog/Moand-and-Functional-Architecture)  
> [Burt.K 님 블로그 - 모나드](https://blog.burt.pe.kr/series/monad-and-functional-architecture-part-3/)

```js
// 모나드
[1];
const g = (a) => a + 1;
const f = (a) => a * a;

console.log(f(g(1)));
// 아래의 함수 합성은 안전하지 않다고 할 수 있다.
console.log(f(g()));

// 모나드: 박스를 가진다.
// 박스 안에 값이 없을 경우 효과가 일어나지 않는다.
Array.of(3)
  .map(g)
  .map(f)
  .forEach((a) => console.log(a));

// Promise는 안 에 값에 대해 안전한 함수 합성이 아니라 비동기 상황에서의 함수 합성이 안전함이다.
Promise.resolve(5)
  .then(g)
  .then(f)
  .then((a) => console.log(a));

new Promise((resolve) => resolve(5))
  .then(g)
  .then(f)
  .then((a) => console.log(a));
```

### Kleisli composition

함수 합성 방법 중 하나이며, 오류가 발생할 수 있는 상황에서 함수 합성을 안전하게 하는 규칙이라고 볼 수 있다.  
아래와 같이 예를 들자면 외부의 배열을 가져다 사용하고 있는데 그 외부의 배열이 변경되게 될 경우 해당하는 인덱스를 찾을 수 없어 `g` 에서 오류가 발생하게 된다.  
이런 경우 `f(g(a)) === g(a)` 가 성립되게 하는 규칙이다.

```js
// 클레이슬리
// f(g(a)) == f(g(a)) 이나 외부변수나 상황에 의해 성립이 안 할 수 있다.
// f(g(a)) == g(a) g에서 에러가 났을 경우 f(g(a)) 와 동일하게 값이 나오는 규칙

const users = [
  { id: 1, name: "a" },
  { id: 2, name: "b" },
  { id: 3, name: "c" },
];

const getUserById = (id) =>
  find((u) => u.id == id, users) || Promise.reject("not found");

const f = ({ name }) => name;
const g = getUserById;
const fg = (id) =>
  Promise.resolve(id)
    .then(g)
    .then(f)
    .catch((a) => a);

const s = f(g(2));
const r = fg(2).then(console.log);

users.pop();
users.pop();

const s2 = f(g(2));
const r2 = fg(2).then(console.log);

console.log(r, r2);
console.log(s, s2);
```

## 비동기 제어

`Promise` 의 경우 아무리 중첩되더라도 `then` 으로 나오는 값은 `Promise` 가 아니다.  
중첩된 경우 중첩된 개수 만큼 `then` 을 사용하는 것이 아니라 한번으로도 값을 반환할 수 있다.

```js
const go = (...args) => reduce((a, f) => f(a), args);
const goPromise = (a, f) => (a instanceof Promise ? a.then(f) : f(a));

const reduce = (f, acc, iter) => {
  if (!iter) {
    iter = acc[Symbol.iterator]();
    acc = iter.next().value;
  } else {
    iter = iter[Symbol.iterator]();
  }
  return goPromise(acc, function recur(acc) {
    let cur;
    while (!(cur = iter.next()).done) {
      acc = f(acc, cur.value);
      if (acc instanceof Promise) {
        return acc.then(recur);
      }
    }
    return acc;
  });
};

go(
  Promise.resolve(1),
  (a) => a + 10,
  (a) => a + 100,
  (a) => a + 1000,
  (a) => Promise.resolve(a + 10000),
  (a) => Promise.reject("Error"),
  (a) => console.log(a)
).catch((a) => console.log(a));
```

## 참조

- [인프런 - 함수형 프로그래밍](https://www.inflearn.com/course/functional-es6)
- [데이블팀 테크 블로그 - 모나드와 함수형 아키텍쳐](https://teamdable.github.io/techblog/Moand-and-Functional-Architecture)
- [Burt.K 님 블로그 - 모나드](https://blog.burt.pe.kr/series/monad-and-functional-architecture-part-3/)
<hr/>

_개인이 참고하고자 작성한 글이며, 잘못된 정보가 있을 수 있습니다. 잘못된 정보는 메일로 보내주시면 감사하겠습니다._ 🙏
