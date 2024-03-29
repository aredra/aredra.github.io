---
title: "모던 자바스크립트 Deep Dive"
date: 2022-12-31
comments: true
read_time: false
toc: true
categories:
  - js
tags:
  - js
---

[모던 자바스크립트 Deep Dive](https://book.interpark.com/product/BookDisplay.do?_method=detail&sc.prdNo=339153552&gclid=Cj0KCQiA_bieBhDSARIsADU4zLcfqxlwZzp4UyhD_D_vJtnTNGyy_Bg324kGM8GcTqDKVRZBJ004_rsaAigaEALw_wcB) 에 대한 개인적인 정리글입니다.

~~HTML 예제가 많이 생략되어 있으니 직접 책을 읽어보시거나 작성해보시길~~

## 40.7 이벤트 위임

예를 들어 ul 태그 안에 li 태그가 30 여개 있다고 가정할 때 각 li 를 클릭 했을 때의 이벤트 핸들러를 등록해야되는데 그렇다면 30 여개를 다 걸지 않게 할 수 있는 방법이 있다. 앞서서 이벤트는 캡쳐링과 버블링이 발생하는 것을 배웠기 때문에 이벤트가 발생한 요소의 상위 요소에서 이벤트를 감지할 수 있음을 알 수 있다.  
이벤트 위임이란 여러 개의 하위 DOM 요소에 각각 이벤트 핸들러를 등록하는 대신 하나의 상위 DOM 요소에 이벤트 핸들러를 등록하는 방법을 말한다.

```js
const $fruits = document.getElementById("fruits");
const $msg = document.querySelector(".msg");

function activate({ target }) {
  if (!target.matches("#fruits > li")) return;
  [...$fruits.children].forEach((fruit) => {
    fruit.classList.toggle("active", fruit === target);
    $msg.textContent = target.id;
  });
}

$fruits.onclick = activate;
```

위 예제처럼 이벤트를 위임할 경우 뜻하지 않은 자식요소에서 이벤트가 발생하여 올라올 수 있으므로 의도한 대로 동작하도록 구별하는 등의 추가 코드가 필요하다.

> 위 예제에서는 target.matches 메서드와 발생한 이벤트 객체로 구별하였다.  
> 일반적으로 target, currentTarget 은 같지만 실제로는 의미하는 바가 다르고 실제로 이벤트 위임등을 하면 다르다.

## 40.8 DOM 요소의 기본 동작 조작

### 40.8.1 DOM 요소의 기본 동작 중단

DOM 요소는 저마다 기본 동작이 있고 이벤트 객체의 `preventDefault` 메서드는 이러한 DOM 요소의 기본 동작을 중단시킨다.

```js
document.getElementsByTagName('a')[0]?.onclick = e => {
  e.preventDefault();
}
```

### 40.8.2 이벤트 전파 방지

이벤트 객체의 `stopPropagation` 메서드는 이벤트 전파를 중단 시킨다. 자신이 받은 이벤트의 전파를 중단 시킨다.

> 캡쳐링 단계에서도 마찬가지이다.

```js
document.querySelector(".container").addEventListener(
  "click",
  function (e) {
    e.stopPropagation();
    console.log("Container");
  },
  true
);
document.querySelector(".btn1").addEventListener("click", function (e) {
  e.stopPropagation();
  console.log("Button1");
});
```

## 40.9 이벤트 핸들러 내부의 this

### 40.9.1 이벤트 핸들러 어트리뷰트 방식

이벤트 핸들러 어트리뷰트 값으로 지정한 문자열이 사실 암묵적으로 생성되는 이벤트 핸들러의 문이라고 앞서서 언급하였다.  
따라서 일반 함수로 호출이 되고 일반적인 경우 this 는 window 객체(전역 객체)를 가리키게 되지만 인수로 this 를 전달할 경우 이벤트를 바인딩한 DOM 요소를 가리킨다.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <div class="container">
      <button class="btn1" onclick="handleClick1()">Button1</button>
      <button class="btn1" onclick="handleClick2(this)">Button2</button>
    </div>
    <script>
      function handleClick1() {
        console.log(this); // window
      }
      function handleClick2(button) {
        console.log(button);
        // <button class="btn1" onclick="handleClick2(this)">Button2</button>
        console.log(this); // window
      }
    </script>
  </body>
</html>
```

### 40.9.2 이벤트 핸들러 프로퍼티 방식과 addEventListener 메서드 방식

두 방식 모두 this 는 이벤트를 바인딩한 DOM 요소를 가리키지만 화살표 함수로 정의할 경우 this 는 핸들러 상위 스코프의 this 를 가리킨다.

> 일반 함수로 등록할 경우 this 는 currentTarget 프로퍼티와 동일하다.

```js
$button1.onclick = function (e) {
  console.logt(e.currentTarget); // $button1
  console.log(this); // $button1
};
$button2.addEventListener("click", function (e) {
  console.logt(e.currentTarget); // $button2
  console.log(this); // $button2
});
```

클래스에서 이벤트 핸들러를 바인딩하는 경우는 this 에 주의해야 한다.

```js
class App {
  constructor() {
    this.$button = document.querySelector(".button");
    this.$button.onclick = this.increase;
  }

  increase() {
    console.log(this); // this.$button 가리킨다.
  }
}
```

이 예제처럼 이벤트 핸들러 내부의 this 는 이벤트를 바인딩한 DOM 요소를 가리키기 때문에 increase 에 바인딩된 this 는 인스턴스가 아니라 this.$button 에 바인딩된다. 이를 막기 위해서는 화살표 함수나 bind 함수를 사용할 필요가 있다.

```js
class App {
  constructor() {
    this.$button = document.querySelector(".button");
    this.$button.onclick = this.increase;

    // 1. this 가 인스턴스에 바인딩 되도록 설정
    this.$button.onclick = this.increase.bind(this);
  }

  // 2. 화살표 함수를 사용
  increase = (e) => {
    console.log(this);
  };
}
```

> addEventListener 방식도 위와 같은 현상이 있다.

## 40.10 이벤트 핸들러에 인수 전달

핸들러 내부에서 함수를 호출하거나 핸들러를 반환하는 함수를 호출하면서 인수를 전달하는 방법이 있다.

```js
const MIN_USER_NAME_LENGTH = 5;
const $input = document.getElementById("input");
const $msg = document.querySelector(".msg");

// 1.
const checkUserNameLength = (min) => {
  $msg.textContent = $input.value.length < min ? "길이가 모자랍니다" : "";
};
$input.onblur = () => checkUserNameLength(MIN_USER_NAME_LENGTH);

// 2.
const checkUserNameLength = (min) => (e) => {
  $msg.textContent = $input.value.length < min ? "길이가 모자랍니다" : "";
};
$input.onblur = checkUserNameLength(MIN_USER_NAME_LENGTH);
```

## 40.11 커스텀 이벤트

### 40.11.1 커스텀 이벤트 생성

이벤트 생성자 함수를 호출하여 명시적으로 이벤트 객체를 생성할 수 있고 임의의 이벤트 타입을 지정할 수 있다. 개발자의 의도로 생성된 이벤트를 커스텀 이벤트라고 한다.  
첫 번째 인수로 이벤트 타입을 문자열로 넘겨주며 새로운 이벤트 타입을 지정하거나 기등록되어 있는 이벤트도 사용이 가능하다.

생성된 커스텀 이벤트 객체는 버블링되지 않으며 preventDefault 메서드로 취소가 불가능하지만 두 번째 인수에 해당 프로퍼티에 대한 설정을 가지는 객체를 전달하면 되며 이 객체에는 이벤트 타입에 따라 가지는 고유의 프로퍼티가 저장이 가능하다.

**커스텀 이벤트는 isTrusted 프로퍼티는 항상 false 이고 2번째 인수로 수정이 불가능하다.**

> 새로운 이벤트 타입을 등록하는 경우는 일반적으로 CustomEvent 이벤트 생성자 함수를 사용한다.

```js
const keyboardEvent = new KeyboardEvent("keyup");
console.log(keyboardEvent.type); // keyup
console.log(keyboardEvent.bubbles); // keyup
console.log(keyboardEvent.cancelable); // keyup

const customEvent = new CustomEvent("foo");
console.log(customEvent.type); // foo

const mouseCustomEvent = new MouseEvent("click", {
  bubbles: true,
  cancelable: true,
  clientX: 50,
  clientY: 100,
  isTrusted: true,
});
console.log(mouseCustomEvent.type); // click
console.log(mouseCustomEvent.bubbles); // true
console.log(mouseCustomEvent.cancelable); // true
console.log(mouseCustomEvent.clientX); // 50
console.log(mouseCustomEvent.isTrusted); // false
```

### 40.11.2 커스텀 이벤트 디스패치

생성한 커스텀 이벤트는 `dispatchEvent` 메서드로 발생시킬 수 있다. 또한 CustomEvent 생성할 때 두 번째 인수에 `detail` 프로퍼티에 원하는 정보를 담은 객체를 넣어서 발생 시킬 수 있다.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <div class="container">
      <button class="btn1">Button1</button>
    </div>
    <script>
      const $button = document.querySelector(".btn1");

      $button.addEventListener("click", (e) => {
        console.log(e);
        // MouseEvent {isTrusted: false, screenX: 0, screenY: 0, …}
      });
      $button.addEventListener("foo", (e) => {
        console.log(e.detail.message); // Hi
      });

      const customMouseEvent = new MouseEvent("click");
      $button.dispatchEvent(customMouseEvent);
      const customFooEvent = new CustomEvent("foo", {
        detail: { message: "Hi" },
      });
      $button.dispatchEvent(customFooEvent);
    </script>
  </body>
</html>
```

> 💿&nbsp; 임의의 이벤트 타입은 프로퍼티나 어브리뷰트 방식을 쓸 수 없다. 해당하는 이벤트가 요소에 등록되어 있지 않기 때문

# 41. 타이머

## 41.1 호출 스케줄링

함수를 명시적으로 호출하면 함수가 즉시 실행된다. 이를 일정 시간이 경과된 후에 실행 시키고 싶다면 타이머 함수를 사용해야 하는데 이를 **호출 스케줄링**이라 한다.  
이러한 타이머 함수는 ECMAScript 스펙이 아니며 브라우저와 노드 환경에서 전역 객체의 메서드로 제공하는 호스트 객체이다.

> 💿&nbsp;자바스크립트 엔진은 단 하나의 실행 컨텍스트 스택을 갖기 때문에 두 가지 이상의 태스크를 동시에 실행할 수 없다.(싱글스레드) 따라서 타이머 함수는 비동기 처리 방식으로 동작한다.

## 41.2 타이머 함수

### 41.2.1 setTimeout / clearTimeout

설정한 시간 뒤 에 한 번만 호출 되는 함수

```js
const timeoutId = setTimeout(func|code[, delay, param1, parma2, ...])
```

setTimeout 함수는 생성된 타이머를 식별할 수 있는 id 를 반환하고 브라우저의 경우 숫자 노드의 경우 객체를 반환하다. 이 id 를 clearTimeout 인수로 전달하면 타이머가 실행되기 전의 경우 타이머를 취소할 수 있다.

```js
const timerId = setTimeout((name) => console.log(`Hi ${name}`), 1000, "SEO");
clearTimeout(timerId);
```

### 41.2.2 setInterval / clearInterval

설정한 타이머 시간 값 마다 반복 호출하는 함수이며 그 외 사항은 setTimeout 과 유사하다.

```js
const timeoutId = setInterval(func|code[, delay, param1, parma2, ...])
```

```js
let count = { i: 0 };

const timerId = setInterval(
  (count) => {
    console.log(count.i++);
    if (count.i > 5) {
      clearInterval(timerId);
    }
  },
  500,
  count
);
```

### 41.3 디바운스와 스로틀

`scroll`, `resize`, `input`, `mousemove` 와 같은 이벤트는 짧은 시간 간격으로 연속해서 발생한다. 이에 바인딩된 이벤트 핸들러가 과도하게 호출되어 성능에 문제을 발생시킬 수 있다. 디바운스와 스로틀은 이러한 과도한 이벤트 핸들러의 호출을 방지하는 기법이다.

```js
const debounce = (func, delay) => {
  let timerId;
  return (event) => {
    if (timerId) {
      clearTimeout(timerId);
    }
    timerId = setTimeout(func, delay, event);
  };
};

const throttle = (func, dealy) => {
  let timerId;
  return (event) => {
    if (timerId) {
      return;
    }
    timerId = setTimeout(() => {
      func(event);
      timerId = null;
    }, delay);
  };
};
```

### 41.3.1 디바운스

짧은 시간 간격으로 연속해서 발생하면 인벤트를 발생시키지 않다고 일정 시간이 경과할 때까지 이벤트가 발생하지 않으면 이벤트가 핸들러가 한 번만 호출하도록 하는 기법이다.

> 자동완성 UI, 버튼 중복 클릭 방지, resize 이벤트 등에 유용하게 사용

### 41.3.2 스로틀

짧은 시간 간격으로 연속해서 발생하더라도 일정 시간 간격으로 이벤트 핸들러를 한번씩만 호출하는 기법이다.

> scroll, 무한 스크롤 UI 구현 등에 유용하게 사용

# 42. 비동기 프로그래밍

## 42.1 동기 처리와 비동기 처리

자바스크립트 엔진은 단 하나의 실행 컨텍스트 스택을 갖는다. 이는 함수가 실행 할 수 있는 창구가 1개이며 2개 이상의 함수를 동시에 실행할 수 없다는 것을 의미한다.  
이처럼 자바스크립트 엔진은 한 번에 하나의 태스크만 실행할 수 있는 싱글 스레드 방식으로 동작한다.  
하나의 태스크가 작업이 종료될 때까지 뒤에 작업들은 대기 상태에 있는다. 이러한 방식을 `동기 처리`라고 하며, 앞서 사용한 타이머 함수들의 경우 뒤의 작업들이 대기 상태에 있지 않고 실행된다. 이를 `비동기 처리`라고 한다.

```js
function foo() {
  console.log("foo");
}
function bar() {
  console.log("bar");
}

function sleep(func, delay) {
  const delayUntil = Date.now() + delay;

  while (Date.now() < delayUntil);
  func();
}

sleep(foo, 3000);
bar();
// foo -> bar

setTimeout(foo, 3000);
bar();
// bar -> foo
```

타이머 함수, HTTP 요청, 이벤트 핸들러는 비동기 처리 방식으로 동작하며, 비동기 처리는 이벤트 루프와 태스크 큐와 깊은 관계가 있다.

## 42.2 이벤트 루프와 태스트 큐

자바스크립트는 싱글 스레드로 하나의 작업밖에 하지 못한다고 앞서 말하였으나 실제로 브라우저를 보면 동시에 처리되는 것 처럼 느껴지는데 이 자바스크립트 동시성을 지원하는 것이 `이벤트 루프`이다.

이벤트 루프는 브라우저에 내장되어 있는 기능 중 하나이며, 배경 설명으로 자바스크립트 엔진들 대부분은 2개 영역을 나뉜다.

- `콜 스택`: 소스코드 평가 과정에서 생성된 실행 컨텍스트가 추가되고 제거는 실행 컨텍스트 스택

- `힙`: 객체가 저장되는 메모리 공간. 콜 스택의 요소인 실행 컨텍스트는 힙에 저장된 객체를 참조한다.

비동기 처리에서 소스코드의 평가와 실행을 제외한 모든 처리는 자바스크립트 엔진을 구동하는 환경(브라우저, 노드)에서 담당한다.  
이를 위해 브라우저 환경은 태스크 큐와 이벤트 루프를 제공한다.

- `태스크 큐`: 비동기 함수의 콜백 함수 또는 이벤트 핸들러가 일시적으로 보관되는 영역이다. 태스크 큐와는 별도로 프로미스 후속 처리 메서드의 콜백 함수가 일시적으로 보관되는 마이크로태스크 큐도 존재한다.

- `이벤트 루프`: 콜 스택에 현재 실행중인 실행 컨텍스트가 있는지 그리고 태스크 큐에 대기 중인 함수가 있는지 반복해서 확인한다. 콜 스택이 비어있고 태스크 큐에 대기 중인 함수가 있다면 이벤트 루프는 순차적으로 태스크 큐에 대기 중인 함수를 콜 스택으로 이동시킨다.

```js
function foo() {
  console.log("foo");
}
function bar() {
  console.log("bar");
}

// 지연시간을 0으로 하여도 4ms 최소 지연 시간을 갖는다.
setTimeout(foo, 0);
bar();
```

위 예제가 실행되는 과정은 아래와 같다.

1. 전역코드가 평가되어 전역 실행 컨텍스트가 생성되고 콜 스택에 푸쉬된다.
2. 전역코드가 실행되기 시작하여 setTimeout 함수가 호출되고 이 실행 컨택스트가 콜스택에 푸쉬되고 실행 중인 컨택스트가 된다.
3. 콜백함수를 스케줄링하고 종료되어 콜 스택에서 팝된다. 이때 호출 스케줄링(타이머 설정)과 만료 시 콜백 함수를 태스크 큐에 푸쉬하는 것은 브라우저의 역할이다.
4. 브라우저의 4-1, 자바스크립트 4-2 가 병행처리된다.  
   4-1. 브라우저는 타이머 설정 및 만료를 기다리고 만료되면 콜백함수 foo 를 태스크 큐로 푸쉬하고 foo 함수는 대기한다.  
   4-2. bar 함수가 호출되어 실행 컨택스트가 생성되고 푸쉬되어 현재 실행 중인 실행 컨택스트가 되고 종료되어 팝된다.
5. 전역코드 실행이 종료되고 전역 실행컨텍스트가 콜 스택에서 팝된다. 이제 콜 스택에 아무런 실행 컨텍스트가 없게 된다.
6. 이벤트 루프에 의해 콜 스택이 비어있음을 감지되고 태스크 큐에서 대기중인 콜백 함수 foo 가 이벤트 루프에 의해서 콜 스택에 푸쉬 된다.

자바스크립트는 싱글 스레드 방식으로 동작하지만 이는 브라우저에 내장된 자바스크립트 엔진이 싱글 스레드 방식으로 동작하는 것이며, 브라우저는 멀티 스레드로 동작한다.

# 43. Ajax

## 43.1 Ajax 란?

자바스크립트를 사용하여 브라우저가 서버에게 비동기 방식으로 데이터를 요청하고, 서버가 응답한 데이터를 쉰하여 웹페이지를 동적으로 갱신하는 프로그래밍 방식이다.

`Ajax` 는 Web API 인 Http 비동기 통신을 위한 메서드와 프로퍼티를 제공하는 `XMLHttpRequest` 객체를 기반으로 동작한다.

과거에는 완전한 HTML을 서버로부터 전송 받아 웹페이지 전체를 처음부터 다시 랜더링하는 방식으로 동작하였고 이런 방식은 아래와 같은 단점을 지니고 있었다.

1. 불필요한 데이터 통신
2. 전체를 랜더링하므로 화면의 깜빡임이 발생한다.
3. 클라이언트와 서버와의 통신이 동기식으므로 서버 응답 전까지 블로킹 처리 된다.

Ajax 를 사용할 경우 위의 방식에 비해 다음과 장점이 있었다.

1. 불필요한 데이터 발생 X
2. 변경이 필요한 부분만 랜더링
3. 클라이언트와 서버가 비동기식으로 동작하기 때문에 블로킹이 발생하지 않음

## 43.2 JSON

클라이언트와 서버 간의 HTTP 통신을 위한 텍스트 데이터 포맷이며 자바스크립트에 종속되지 않는 언어 독립형 데이터 포맷으로 대부분의 프로그래밍 언어에서 사용할 수 있다.

### 43.2.1 JSON 표기 방식

키와 값으로 구성된 순수 텍스트, 키와 문자열은 반드시 큰 따옴표 `"` 로 묶어야 한다.

```js
{
  "name": "Seo",
  "age": 20,
  "hobby": ["Piano"]
}
```

### 43.2.2 JSON.stringify

JSON.stringify 메서드는 객체를 JSON 포맷의 문자열로 변환한다. 클라이언트가 서버로 객체를 전송하려면 객체를 문자열화해야 하는데 이를 `직렬화`라고 한다.

```js
JSON.stringify(value[, replacer[, space]])
```

```js
const person = {
  name: "Seo",
  age: 20,
  hobby: ["Piano"],
};

const json = JSON.stringify(person, null, 2);
console.log(typeof json, json);

function filter(key, value) {
  return typeof value === "number" ? undefined : value;
}
const filteredJson = JSON.stringify(person, filter, 2);
console.log(typeof filteredJson, filteredJson);
```

### 43.2.3 JSON.parse

JSON 포맷의 문자열을 객체로 변환한다. 문자열을 객체로서 사용하기 위해 JSON 포맷의 문자열을 객체화하는데 이를 `역직렬화`라고 한다.

```js
JSON.parse(text[, reviver])
```

```js
const doubleObj = JSON.parse('{"p": 5, "name": "SEO"}', (key, value) =>
  typeof value === "number" ? value * 2 : value
);
console.log(doubleObj); // {p: 10, name: 'SEO'}
```

## 43.3 XMLHttpRequest

자바스크립트를 사용하여 HTTP 요청을 전송하려면 XMLHttpRequest 객체를 사용한다.

### 43.3.1 XMLHttpRequest 객체 생성

생성자 함수를 호출하여 생성한다. 브라우저 환경에서만 정상적으로 동작한다.(Web API)

### 43.3.2 XMLHttpRequest 객체의 프로퍼티와 메서드

#### 프로토타입 프로퍼티

- readyState
- status
- statusText
- responseType
- response
- responseText

#### 이벤트 핸들러 프로퍼티

- onreadystatechange
- onloadstart
- onprogress
- onabort
- onerror
- onload
- ontimeout
- onloaded

#### 메서드

- open
- send
- abort
- setRequestHeader
- getResponseHeader

#### 정적 프로퍼티

- UNSENT
- OPENED
- HEADERS_RECEIVED
- LOADING
- DONE

### 43.3.3 HTTP 요청 전송

요청을 전송하는 경우 다음 순서를 따른다.

1. XMLHttpRequest.prototype.open 메서드로 HTTP 메서드 요청을 초기화한다.
2. 필요에 따라 특정 HTTP 요청의 헤더 값을 설정한다.
3. XMLHttpRequest.prototype.send 메서드로 HTTP 요청을 전송한다.

```js
const xhr = new XMLHttpRequest();

xhr.open("GET", "/users");
xhr.setRequestHeader("content-type", "application/json");
xhr.send();
```

#### XMLHttpRequest.prototype.open

서버에 전송할 HTTP 요청을 초기화한다.

```js
xhr.open(method, url[, async])
```

<table>
  <thead>
    <tr>
      <th>메서드</th>
      <th>종류</th>
      <th>목적</th>
      <th>페이로드</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>GET</td>
      <td>index/retrieve</td>
      <td>모든/특정 리소스 취득</td>
      <td>X</td>
    </tr>
    <tr>
      <td>POST</td>
      <td>create</td>
      <td>리소스 생성</td>
      <td>O</td>
    </tr>
    <tr>
      <td>PUT</td>
      <td>replace</td>
      <td>리소스 전체 교체</td>
      <td>O</td>
    </tr>
    <tr>
      <td>PATCH</td>
      <td>modify</td>
      <td>리소스 일부 교체</td>
      <td>O</td>
    </tr>
    <tr>
      <td>DELETE</td>
      <td>delete</td>
      <td>모든/특정 리소스 삭제</td>
      <td>X</td>
    </tr>
  </tbody>
</table>

#### XMLHttpRequest.prototype.send

open 메서드로 초기화된 HTTP 요청을 서버에 전송한다. 요청 메서드에 따라 전송 방식에 차이가 있다.  
요청 몸체에 페이로드를 인수로 전달할 수 있는데 객체인 경우 직렬화를 반드시 해줘야 한다. 요청 메서드가 GET 일 경우는 무시되고 null 로 설정이 된다.

```js
xhr.send(JSON.stringify({ id: 1, content: "HTML" }));
```

#### XMLHttpRequest.prototype.setRequestHeader

특정 HTTP 요청의 헤더를 설정한다. 반드시 open 이후에 호출해야 된다.

```js
const xhr = new XMLHttpRequest();

xhr.open("POST", "/users");
xhr.setRequestHeader("content-type", "application/json");
// accept 미설정 시 */* 로 전송
xhr.setRequestHeader("accept", "application/json");
xhr.send(JSON.stringify({ id: 1, content: "HTML" }));
```

### 43.3.4 HTTP 응답처리

응답을 처리하기 위해서는 XMLHttpRequest 객체가 발생시키는 이벤트를 캐치해서 처리할 수 있다.

```js
const xhr = new XMLHttpRequest();

xhr.open("GET", "https://jsonplaceholder.typicode.com/todos/1");
xhr.send();

// 응답처리, load 이벤트로도 대체 가능하다.
xhr.onreadystatechange = () => {
  if (xhr.readyState !== XMLHttpRequest.DON) {
    return;
  }
  if (xhr.status === 200) {
    console.log(JSON.parse(xhr.response));
  } else {
    console.error("Error", xhr.status, xhr.statusText);
  }
};
```

# 44. REST API

**REST는 HTTP 를 기반으로 클라이언트가 서버의 리소스에 접근하는 방식을 규정한 아키텍처**이고, REST API 는 REST 를 기반으로 서비스 API 를 구현한 것을 의미한다.

## 44.1 REST API 구성

`자원`, `행위`, `표현` 3가지 요소로 구성된다. REST 는 자체 표현 구조로 구성되어 REST API 만으로 HTTP 요청의 내용을 이해할 수 있다.

<table>
  <thead>
    <tr>
      <th>구성 요소</th>
      <th>내용</th>
      <th>표현 방법</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>자원</td>
      <td>자원</td>
      <td>URI(엔드포인트)</td>
    </tr>
    <tr>
      <td>행위</td>
      <td>자원에 대한 행위</td>
      <td>HTTP 요청 메서드</td>
    </tr>
    <tr>
      <td>표현</td>
      <td>자원에 대한 행위의 구체적 내용</td>
      <td>페이로드</td>
    </tr>
  </tbody>
</table>

## 44.2 REST API 설계 원칙

1. URI 는 리소스를 표현하는데 집중
   - 리소스를 식별할 수 있는 이름은 동사보다 명사를 사용
2. 행위에 대한 정의는 HTTP 요청 메서드를 통해서
   - 주로 5가지 요청 메서드를 사용하며 행위는 URI 로 표현하지 않는다.

```sh
# bad
GET /todos/delete/1
GET /getTodos/1
GET /todos/show/1

# good
DELETE /todos/1
GET /todos/1
```

## 44.3 JSON Server 를 이용한 REST API 실습

[JSON-SERVER NPM](https://www.npmjs.com/package/json-server)  
책에서는 아래의 순서로 안내하고 있다.

```sh
mkdir project-name && cd project-name
npm init -y
npm install json-server --save-dev
touch db.json
json-server --watch db.json
```

근데 정확한 사유는 모르겠지만 json-server 명령어를 못 찾는다고 나와서 그냥 글로벌로 설치하고 돌려서 진행하였다.💦  
파일명 역시 꼭 db.json 으로 할 필요 없다. 또한 꼭 같은 프로젝트에서만 요청할 필요도 없다.

```js
const xhr = new XMLHttpRequest();
xhr.open("GET", "http://localhost:3030/todos/1");

// MIME 타입 설정
// xhr.setRequestHeader("content-type", "application/json");

// 페이로드가 필요한 경우 send 메서드 인수에 직렬화하여 넣는다.
xhr.send();
xhr.onload = () => {
  if (xhr.status === 200) {
    console.log(xhr.response);
  } else {
    console.error(xhr.status);
  }
};
```

위와 같은 식으로 실습을 진행할 수 있고 POST, PUT, PATCH 는 전송할 페이로드의 MIME 타입을 지정하는 것 외에 특이사항이 없어 생략한다.

<hr/>

_개인이 참고하고자 작성한 글이며, 잘못된 정보가 있을 수 있습니다. 잘못된 정보는 메일로 보내주시면 감사하겠습니다._ 🙏
