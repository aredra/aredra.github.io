---
title: "JS - Web Workers"
date: 2021-10-22
comments: true
read_time: false
toc: true
categories:
  - JS
tags:
  - web-workers
---

JS 라고 카테고리에 넣기는 하였지만 `Web workers` 는 `JS`가 아니라 `Web API` 이다.  
하나의 스레드에서 동작하는 JS에서 복수의 스레드로 비동기로 실행할 수 있는 방법 중 하나이다.

## Web Workers?

`생성자 Worker()` 를 통해 생성되는 객체이며 Worker에서 동작하는 코드는 현재의 `window` 와는 `다른 전역 컨텍스트`를 가진다.  
한 개의 스크립트에서 접근할 수 있는 `Dedicated Worker` 와 복수의 스크립트에서 접근할 수 있는 `Shared Worker` 가 있다.  
Worker 는 DOM 조작은 불가능하며, API 일부는 사용할 수 없다.  
호출한 스크립트와는 `postMessage` 로 요청 `onmessage` 로 응답을 수신할 수 있다.

```js
// workers.js
self.onmessage = (event) => {
  self.postMessage("정상 수신 후 메인으로 전송");
};

// main.js
const main = new Worker("worker.js");
main.postMessage("메인에서 요청");
main.onmessage = (event) => {
  console.log(event.data); // 정상 수신 후 메인으로 전송
};
```

위 예제는 `Dedicated Worker` 를 생성한 것으로 이렇게 되면 `main.js` 에서만 사용할 수 있다.  
`Shared Worker` 를 생성하기 위해서는 `new SharedWorker('worker.js')` 로 생성하면 된다.

## WorkerGlobalScope

- `Worker` 의 전역 객체로 이벤트 객체를 상속받는다.
- `self`: `WorkerGlobalScope를` 참조한다.
- `self.importScripts()`: 1개 이상의 js 파일을 동기방법으로 Wor`ker 객체에 삽입할 수 있다.
- `self`를 생략해도 되지만 보편적으로 작성한다.

```js
// message.js
const SUCCES_MESSAGE = "success";
const getSuccessMessage = (requestName) => {
  return requestName + SUCCES_MESSAGE;
};

// worker.js
self.importScripts("message.js");
self.onmessage = (event) => {
  const message = getSuccessMessage(event.data);
  self.postMessage(message);
};

// main.js
const main = new Worker("worker.js");
main.postMessage("Request1");
main.onmessage = (event) => {
  console.log(evnet.data); // Request1 success
};
```

## Transferable Object

오브젝트에 대한 소유권을 넘겨줄 때 사용되는 오브젝트이다.  
`Worker` 는 다른 쓰레드에서 동작하기 때문에 일반적으로 값이 공유되지 않으며 이에 따라 기본적으로 `postMessage` 파라미터에 작성한 오브젝트의 값을 복사하여 전송을 하게 되며, 복사하게 때문에 당연히 연동되지 않는다. (**참조가 아닌 복사이다 보니 오브젝트가 크면 클수록 복사하는 시간도 증가한다.**)  
postMessage 의 2번째 파라미터에 소유권을 넘겨둘 객체들을 배열에 담아보내주면 되고 넘겨줄 시 넘겨준 곳에서는 사용할 수 없다.

> main.postMessage(data, transferList);

## 참고자료

- [MDN - Web workers](https://developer.mozilla.org/ko/docs/Web/API/Web_Workers_API/Using_web_workers)
- [인프런 - JS ES6+](https://www.inflearn.com/course/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-es6-%EC%8B%AC%ED%99%94#)

<hr/>

_개인이 참고하고자 작성한 글이며, 잘못된 정보가 있을 수 있습니다. 잘못된 정보는 메일로 보내주시면 감사하겠습니다._ 🙏
