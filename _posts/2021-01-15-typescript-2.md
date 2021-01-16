---
title: "TypeScript type alias - interface"
date: 2021-01-15
comments: true
read_time: false
toc: true
categories:
  - TypeScript
tags:
  - type alias
  - interface
---

<br>
Typescript에서는 타입별칭(Type Alias)와 인터페이스를 통해 각 타입를 지정하여 편리하게 사용할 수 있다.

## Interface

상호 간의 정의한 약속, 규칙, 명세를 의미한다. 아래와 같이 작성할 수 있다.

```js
interface Person {
  name: string;
  readonly job: string;
  age?: number;
}

class student implements Person {
  name: string = 'aredra';
  job: string = 'student';
  age: number = 99;
  constructor() {}
}

interface sum {
  (a: number, b: number): number;
}
```

## 타입 별칭(Type alias)

특정 타입을 만들어 놓고 별칭을 붙이는 형태

```js
type Developer = {
  name: string,
  skill: string,
};
```

## 둘의 차이란?

상속을 통한 확장 가능 여부로 알고 있다. 현재에는 타입 별칭의 기능이 확장되고 있다고는 하는데 좀더 확인이 필요한 부분이다.
