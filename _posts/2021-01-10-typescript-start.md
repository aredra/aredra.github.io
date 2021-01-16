---
title: "TypeScript 시작하기"
date: 2021-01-10
show_date: true
comments: true
read_time: false
toc: true
categories:
  - TypeScript
tags:
  - typescript
  - js
---

공부해야지.. 하던 `TypeScript`를 이제 막 시작하였다. 완전 신생아지만 `java` 같은 느낌이 든다.

### TypeScript란?

자바스크립트의 Superset이고, 그냥 자바스크립트처럼 확장된 기능을 사용한다면 브라우저에 바로 실행 할 수 없고 babel과 같이 변환과정(Complie, Transpile)이 필요하다.

아래의 명령어로 설치 및 변환을 할 수 있다.

```
npm i -g typescript
tsc filename.ts
```

추가적으로 TypeScript 확장도구를 설치하면 좀 더 편의기능을 쓸 수 있다.

### TypeScript 사용 이유

아래와 같은 이유가 있고, 솔직히 아직은 잘 모르겠지만 개발에 사용하다보면 큰 차이가 날 것 같다.

- 에러의 사전 방지
- 개발 생산성 향상

### JavaScript로 유사하기 사용하기

기존 JS로도 아래와 같은 방법으로 유사하게 사용은 가능하다.

```js
// @ts-check

/**
 * @typedef {object} User
 * @property {string} name
 * @property {string} email
 */

/**
 *
 * @param {number} a 첫번째 숫자
 * @param {number} b 두번째 숫자
 * @returns {number}
 */
function add(a, b) {
  return a + b;
}
```
