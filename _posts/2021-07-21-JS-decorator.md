---
title: "JS - Decorator"
date: 2021-07-21
comments: true
read_time: false
toc: true
categories:
  - JS
tags:
  - decorator
---

머지않은 과거에 횡단관심사에 대해 끄적였다. 관련하여 JS에서는 `Proxy` 객체와, `Decorator` 등을 사용하여 비슷한 점이 있는 것 같아 메모한다. **Decorator만** 💦

## Decorator

인자로 받은 함수의 행동을 변경시켜주는 함수를 데코레이터 라고 부른다.

> 참고: [모던 자바스크립트 - 데코레이터](https://ko.javascript.info/call-apply-decorators)

현재 JS 문법만으로 구현이 가능하지만 좀 더 편리하게 사용하기 위해 현재 제안이 진행중이다. `Stage: 2(초고)` 상태이다. `Babel` 을 사용하면 현재도 사용가능하다.  
추가적으로 이런 제안상태인 친구들을 `ESNext` 라고도 한다.

> 참고: [TC39 - proposal-decorators](https://github.com/tc39/proposal-decorators)

여기서는 현재 문법만을 이용하려한다.

## 예제

간단한 캐쉬 데코레이터 이다.

> 출처: [모던 자바스크립트 - 데코레이터](https://ko.javascript.info/call-apply-decorators)

```js
let worker = {
  slow(min, max) {
    alert(`slow(${min},${max})을/를 호출함`);
    return min + max;
  },
};

function cachingDecorator(func, hash) {
  let cache = new Map();
  return function () {
    let key = hash(...arguments);
    if (cache.has(key)) {
      return cache.get(key);
    }

    let result = func.call(this, ...arguments);

    cache.set(key, result);
    return result;
  };
}

function hash() {
  return [].join(arguments);
}

worker.slow = cachingDecorator(worker.slow, hash);

alert(worker.slow(3, 5));
alert(worker.slow(3, 5)); // 캐시된 결과 출력
```
