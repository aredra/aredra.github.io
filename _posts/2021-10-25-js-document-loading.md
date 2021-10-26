---
title: "JS - 문서와 리소스 로딩"
date: 2021-10-25
comments: true
read_time: false
toc: true
categories:
  - JS
tags:
  - document
  - loading
---

브라우저에서 문서를 열 때 작업 진행 정도에 따라 발생하는 이벤트가 있고 이를 활용하면 많은 것을 할 수 있다.  
간단하게 작성내용이 있는 페이지의 경우 이탈할 때 발생하는 경고창 등이 있겠다.  
이와 같은 문서를 로딩할 때 발생하는 이벤트에 대한 메모.

## 발생하는 이벤트

발생하는 이벤트들은 간단하게는 아래와 같다.

- `DOMContentLoaded` : DOM 트리 구성이 완료되었을 때 `document` 객체에서 발생되고 이 단계에서는 이미지나 스타일시트 등 기타 리소스는 로드 중일 수 있다.
- `load` : DOM 트리가 완성되고 이미지 등 외부 리소스들도 모두 로드되었을 때 `window` 객체에서 발생된다.
- `beforeunload` : 사이트를 떠나려 할 때 `window` 객체에서 발생한다.
- `unload` : 사이트를 떠날 때 `window` 객체에서 발생한다.

## DOMContentLoaded

DOM 트리 구성이 완료된 후 발생하는 이벤트 해당 이벤트에서는 모든 요소들에 접근이 가능하게 된다. 하지만 이미지등의 외부 자원은 불러오는 중일 수 있기 때문에 이미지의 사이즈 등은 알 수 없다. 해당 이벤트는 일반적인 경우 스크립트가 모두 실행된 뒤 발생하게 되며, 브라우저의 자동완성 기능이 DOMContentLoaded 에서 일어나게 된다.  
addEventListener 를 사용하여 이벤트를 다룰 수 있다.

> ### 🚦 추가정보
>
> - async 속성이 있는 스크립트와 동적으로 생성되어 추가된 스크립트는 DOMContentLoaded 를 막지 않는다.
> - 일반적으로 DOMContentLoaded는 스타일시트 로드를 기다리지 않으나 스타일시트 하단에 바로 스크립트가 있을 경우 스크립트가 스타일시트가 로드 되기 전까지 실행되지 않아서 스타일시트의 로드 역시 기다리게 된다.

```js
document.addEventListener("DOMContentLoaded", () => {
  console.log(">>>>>>>>>>>>>> DOMContentLoaded!");
});

console.log(">>>>>>>>>>>>>> Script");

// >>>>>>>>>>>>>> Script
// >>>>>>>>>>>>>> DOMContentLoaded!
```

## load

window 객체의 load 이벤트는 스타일, 이미지 등의 리소스가 모두 로드 되었을 때 발생하며, window 객체에 onload 프로퍼티와 addEventListener 를 통해 사용할 수 있다. 이미지가 로드되었기 때문에 이미지의 사이즈 등을 알 수 있다.

```js
window.onload = () => {
  console.log(">>>>>>>>>>>>>> load!");
  const img = document.getElementById("img-id");
  alert(`>>>>>>>>>>>>>> 이미지 사이즈: ${img.offsetWidth}`);
};

<img id="img-id" src="https://http.cat/100" alt="100status-cat" />;

// >>>>>>>>>>>>>> load!
// >>>>>>>>>>>>>> 이미지 사이즈: 100
```

## beforeunload

사용자가 현재 페이지를 떠나려 할 때 window 객체에서 발생하는 이벤트로 현재 페이지에 작성중인 내용이 있을 경우 해당 내용이 사라지는 등의 안내를 할 때 많이 사용된다.
`return false` 를 통해 브라우저별 경고창을 확인 할 수 있고 해당 이벤트에서 `alert`, `confirm` 등을 사용할 수 없다.

```js
window.onbeforeunload = () => {
  console.log(">>>>>>>>>>>>>> beforeunload!");
  return false;
};
```

## unload

사용자가 페이지를 떠날 때 발생하는 window 객체의 이벤트로 팝업창을 닫는 것과 같은 딜레이가 없는 작업을 수행할 수 있다. 일반적으로 사용자의 행동을 분석한 분석정보를 많이 보내는데 사용된다.
`navigator.sendBeacon(url, data)` 를 통해 백그라운드에서 전송할 수 있다.

> ### 🚦 navigator.sendBeacon(url, data) 추가정보
>
> - 요청은 POST 메서드로 전송됨
> - 문자열 외에 폼이나 fetch에서 설명하는 기타 포맷들도 보낼 수 있으나 일반적으로 문자열 형태의 객체가 전송됨
> - 전송 데이터는 64kb를 넘을 수 없다.

```js
let analyticsData = { /* 분석 정보가 담긴 객체 */ };

window.addEventListener("unload", function() {
  navigator.sendBeacon("/analytics", JSON.stringify(analyticsData));
};
```

## 참고자료(원문)

- [모던 javascript 튜토리얼](https://ko.javascript.info/onload-ondomcontentloaded)

<hr/>

_개인이 참고하고자 작성한 글이며, 잘못된 정보가 있을 수 있습니다. 잘못된 정보는 메일로 보내주시면 감사하겠습니다._ 🙏
