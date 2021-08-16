---
title: "JS - Promise"
date: 2021-08-16
comments: true
read_time: false
toc: true
categories:
  - JS
tags:
  - promise
---

`JS`는 기본적으로 동기로 실행이 되며 비동기로 실행시키기 위해서 현재는 `Promise` 가 흔히 사용되고 있다.  
이 `Promise` 에 대한 간략히 메모해보려한다.

## Promise

> new Promise(function(resolve, reject) {...})

기존에는 `DOM` 스펙에 있었으나 `Javascript` 스펙으로 옮겨져 왔다.

상태는 다음과 같이 3가지가 있고 하나만 발생한다.

  - `Pending`: Promise 인스턴스를 생성하고 결과가 아직 안 나왔을 때
  - `Settled(fulfilled)`: `Pending` 이 종료되고 결과가 resolve 일 때
  - `Settled(rejected)`: `Pending` 이 종료되고 결과가 reject 일 때

```js
const promiseObj = new Promise((resolve, reject) => {
  resolve();
  // 상태는 하나만 발생한다. 위에서 fulfilled 가 되었으므로 reject()는 호출되지 않는다.
  reject(); 
  console.log('Promise');
});

promiseObj.then(
  // 첫번째 인자는 fullfiled 일때 호출
  value => console.log('Success'), 
  // 두번째 인자는 rejected 일때 호출
  error => console.log('Fail')
);

console.log('End');

// Promise
// End
// Success
```

## then()

> PromiseObj.then(fufilled function, rejected function)

성공과 실패 핸들러 함수를 작성하며 `Promise 인스턴스`를 반환하고 `return 하는 값`의 경우는 [[PromiseValue]]에 설정하여 넘겨준다.

```js
const promiseObj = new Promise((resolve, reject) => {
  resolve(100);
});

promiseObj.then(value => value += 100)
          .then(value => console.log(value));

// 200
```

## catch()

`then` 의 두번째 파라미터에 `reject` 의 핸들러 함수를 작성하지 않고 사용할 수 있는 메소드이다.  
`then` 과 같이 `Promise 인스턴스`를 반환하고 `return 값`은 [[PromiseValue]]에 설정 되며 정상적으로 처리 될 경우 다음 체이닝에서 then의 `fullfilled의 핸들러 함수`에 전달 된다.

```js
const promiseObj = new Promise((resolve, reject) => {
  resolve(100);
});

promiseObj.then(value => {throw 'Error'})
          .then(value => console.log(value))
          .catch(error => {
            console.log(`Catch1: ${error}`);
            return 200
          }).then(value => console.log(value))
          .catch(error => console.log(`Catch2: ${error}`));

// Catch1: Error
// 200
```

## finally()

성공과 실패 상태와 상관없이 실행되며 `ES2018`부터 지원하며 파라미터를 받아도 상관없으나 `undefined` 가 출력되고 Promise 인스턴스를 반환한다.

```js
const promiseObj = new Promise((resolve, reject) => {
  resolve(100);
});

const temp = promiseObj.then(value => {throw 'Error'})
                      .then(value => console.log(value))
                      .catch(error => {
                        console.log(`Catch1: ${error}`);
                        return 200
                      }).finally(param => {
                        console.log(`Finally: ${param}`);
                      });

// Catch1: Error
// Finally: undefined
```

## resolve()
`fullfilled` 상태의 Promise 인스턴스를 생성하여 반환한다.

```js
const promiseObj = Promise.resolve({name: 'Aredra', age: 9999});

promiseObj.then(({name, age}) => console.log(name, age));

// Aredra 9999
```
## reject()
`rejected` 상태의 Promise 인스턴스를 생성하여 반환한다.

```js
const promiseObj = Promise.reject('Error');

promiseObj.then(({name, age}) => console.log(name, age))
          .catch(error => console.log(error));

// Error          
```

## thenable()

Promise.resolve() 파라미터에 then() 을 작성한 형태

```js
const promiseObj = Promise.resolve({
  then(resolve, reject) {
    resolve({name: 'Aredra', age: 9999});
  }
});

promiseObj.then(({name, age}) => console.log(name, age));

// Aredra 9999
```