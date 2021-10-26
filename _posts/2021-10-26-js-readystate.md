---
title: "JS - readyState, defer, async.."
date: 2021-10-26
comments: true
read_time: false
toc: true
categories:
  - JS
tags:
  - readyState
---

작일에 작성된 [JS - 문서와 리소스 로딩](./2021-10-25-js-document-loading.md)과 해당 글은 순수하게 [모던 JavaScript 튜토리얼 - 문서와 리소스 로딩](https://ko.javascript.info/loading)를 보고 개인적으로 정리한 글로 자세한 내용은 좌측의 링크를 통해서 보시는게 더 좋습니다.

## readyState

문서가 모든 리소스를 다 읽은 후 DOMContentLoaded 나 load 이벤트에 핸들러가 등록된다면 핸들러가 실행되는 일은 없을 것이다.  
이러한 상황에 사용할 수 있는 프로퍼티가 document.readyState 이다.  
현재 문서의 로딩 상태를 알려주는 프로퍼티로 아래와 같이 3가지 상태가 있다.

- `loading` : 문서를 불러오는 중
- `interative` : 문서가 완전히 불러와졌을 때 (이미지 등 외부 리소스 제외) - `DOMContentLoaded` 와 근접한 상태
- `complete` : 문서와 외부 리소스를 모두 불러왔을 때 - `load` 와 근접한 상태

`readyState` 의 상태변경은 `readystatechange` 이벤트를 통해서 감지할 수 있다.

```html
<script>
  function log(txt) {
    console.log(`${txt} : ${Math.floor(performance.now())}`);
  }

  log("initial readyState:" + document.readyState);

  document.addEventListener("readystatechange", () =>
    log("readyState:" + document.readyState)
  );
  document.addEventListener("DOMContentLoaded", () => log("DOMContentLoaded"));

  window.onload = () => log("window onload");
</script>

<iframe src="iframe.html" onload="log('iframe onload')"></iframe>

<img src="http://en.js.cx/clipart/train.gif" id="img" />
<script>
  img.onload = () => log("img onload");
</script>

<!-- 
initial readyState:loading : 4131  
readyState:interactive : 4139  
DOMContentLoaded : 4139  
iframe onload : 4521  
img onload : 11041 
readyState:complete : 11042 
window onload : 11042 
-->
```

> 위의 원본 코드는 [plnkr.co](https://plnkr.co/edit/sX1w7BGpMxfCERwq?p=preview&preview)에서 확인 가능합니다.

로그를 보면 (단위: ms) `interactive` 와 `DOMContentLoaded` 가 거의 동시에, `complete` 와 `onload` 가 거의 동신에 실행되는 것을 확인할 수 있다.

## defer, async, 동적 스크립트

일반적으로 브라우저는 HTML을 읽다가 스크립트를 만나면 스크립트를 먼저 실행하며 DOM 생성은 중단되게 된고 스크립트는 자신에 라인보다 밑에 있는 DOM 요소는 가져올 수 없으며 사용자 또한 볼 수 없다.  
이러한 불편함을 막고자 보통 스크립트를 하단에 위치시키지만, 문서와 스크립트가 사이즈가 클 경우 속도의 이슈는 해결되지 않고 이러한 문제를 해결하기 위해서 `defer`, `async` 속성을 사용할 수 있다.

### defer

`defer` 속성이 있는 스크립트는 **백그라운드**에서 다운로드가 진행된다. 이게 무슨 의미인가 하면 HTML 파싱이 중단되지 않는다는 것을 의미한다.  
스크립트의 실행은 페이지의 구성이 끝날 때 DOM이 준비되고 `DOMContentLoaded` 이벤트가 발생하기 전이 이루어진다.  
백그라운드에서 다운로드가 일어나므로 작은 크기의 스크립트가 먼저 다운로드 될 수는 있지만 실행은 HTML에 추가된 순서대로 이루어진다.

> #### 🚦 추가정보
>
> - defer 속성은 src 가 있을 경우에만 유효하다.

### async

완전히 독립적으로 실행되는 스크립트이다. `DOMContentLoaded` 보다 먼저 다운로드가 완료될 경우 먼저 실행되면 늦게 될 경우 늦게 실행된다.~~(진정한 마이웨이!!!)~~  
또한 다른 스크립트들을 기다리지 않는다. async 속성이 있는 스크립트가 여러개 있을 경우 추가된 순서대로 실행되는 것이 아닌 먼저 다운로딩이 끝난 순서대로 실행된다.

### 동적 스크립트

동적 스크립트는 기본적으로 async 속성을 지닌 것으로 동작하지만 async 프로퍼티를 통해서 추가된 순서대로 동작하게끔 할 수 있다.

```js
const loadScript = (src, async = true) => {
  const script = document.createElement("script");
  script.src = src;
  script.async = async;
  document.body.appendChild(script);
};

loadScript("/script1.js", false);
loadScript("/script2.js", false);
```

## 스크립트 관련 기타 기능

`onload` 와 `onerror` 프로퍼티를 사용하여 스크립트가 다운로드되거나 에러가 발생하면 이벤트에 핸드러를 작성할 수 있다.

```js
const script = document.createElement("script");

script.src = "./myUtils.js";
document.body.append(script);

script.onload = () => {
  console.log(VERSION);
};

script.onerror = () => {
  console.error("Error loading " + this.src);
};
```

> #### 🚦 추가정보
>
> - 위의 onload/onerror 는 로딩 자체만 추적
> - 스크립트의 실행 과정에서 발생하는 에러는 캐치하지 못 하며 전역 처리기 window.onerror 를 사용해야 합니다.

다른 오리진의 스크립트를 가져오는 경우 `crossorigin` 속성을 사용해야 한다.  
물론 서버에서도 요청하는 오리진에 대한 허용이 되어 있어야 한다.

- 속성이 없을 경우 : 접근 불가
- `crossorigin="anonymous"` : 서버 측에서 `Access-Control-Allow-Origin` 헤더에 \* 또는 요청자의 origin 으로 응답하는 경우 액세스가 허용되고, 브라우저는 인증 정보와 쿠키를 서버로 보내지 않는다.
- `crossorigin="use-credentials"` : 서버가 `Access-Control-Allow-Origin` 에 우리 도메인이 포함되고 `Access-Control-Allow-Credentials: true` 인 헤더 을 다시 보내는 경우 액세스가 허용되고 브라우저는 인증 정보와 쿠키를 서버로 보낸다.

## 참고자료(원문)

- [모던 javascript 튜토리얼](https://ko.javascript.info/loading)

<hr/>

_개인이 참고하고자 작성한 글이며, 잘못된 정보가 있을 수 있습니다. 잘못된 정보는 메일로 보내주시면 감사하겠습니다._ 🙏
