---
title: "JS - Promise all, race"
date: 2021-08-17
comments: true
read_time: false
toc: true
categories:
  - JS
tags:
  - promise
---

## all()

> Promise.all(iterable Object)

파라미터의 모든 `Promise` 처리를 완료했을 때 `then` 의 핸들러 함수를 실행한다. 파라미터에 작성된 순서대로 `Promise` 의 인스턴스는 생성되고 실행되는 순서는 각 요청에 따라 다를 수 있다.  
모든 요청이 종료되었으면 `then()` 이 실행되면서 각 파라미터의 `resolve` 값을 파라미터에 **작성된 순서**로 설정한다.

```js
const promiseDelay = (delay) => {
  return new Promise(resolve => {
    setTimeout(() => {
      console.log(delay);
      resolve(delay);
    }, delay)
  })
}

Promise.all([
          promiseDelay(700),
          promiseDelay(300), 
          promiseDelay(500)
        ]).then(param => console.log(param));

// 300
// 500
// 700
// [700, 300, 500]        
```

실행 도중 `reject` 가 발생하게 되면 모든 요청이 끝나기를 기다리지 않고 `reject` 가 발생한 시점에 `rejected` 상태에 대한 핸들러 함수를 실행하게 되며 `fulfilled` 상태에 대한 핸들러 함수는 호출 되지 않는다.  
파라미터에 등록된 함수는 모두 실행되기는 한다. 중간에 종료되거나 하지 않는다.


```js
const promiseDelay = (delay) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log(delay);
      delay === 300 ? reject(delay) : resolve(delay);
    }, delay)
  })
}

Promise.all([
          promiseDelay(700),
          promiseDelay(300), 
          promiseDelay(500)
        ])
        .then(param => console.log(param))
        .catch(error => console.log(`Rejected: ${error}`));

// 300
// Rejected: 300
// 500
// 700        
```

## race()

> Promise.race(iterable Object)

`reject`, `resolve` 와 관계 없이 처음 한번만 실행하고 더 이상 실행하지 않는다.  
**첫 응답이 reject 라면 rejected 상태의 핸들러 함수가 호출되고 더이상 실행되지 않는다.**

```js
const promiseDelay = (delay) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log(delay);
      delay === 500 ? reject(delay) : resolve(delay);
    }, delay)
  })
}

Promise.race([
          promiseDelay(700),
          promiseDelay(300), 
          promiseDelay(500)
        ])
        .then(param => console.log(`First resolved: ${param}`))
        .catch(error => console.log(`Rejected: ${error}`));
```