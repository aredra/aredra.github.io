---
title: "JS - 함수형 프로그래밍"
date: 2021-07-27
comments: true
read_time: false
toc: true
categories:
  - JS
tags:
  - functional
---

함수형 프로그래밍 흔히 듣게되는 기법이다. 관련하여 기초적인 부분을 정리해본다.

# 1급 시민

프로그래밍 언에서 1급 시민이 되기 위한 조건은 3가지이다.

- 변수에 대입할 수 있다.
- 인자에 대입할 수 있다.
- 반환값에 대입할 수 있다.

함수형 프로그래밍을 지원하기 위해서는 해당 프로그래밍 언어에서 함수가 1급 시민이여야 하고 자바스크립트의 경우는 위 조건을 충족하기 때문에 함수형 프로그래밍을 지원한다고 말할 수 있다.

# 선언형 프로그래밍

함수형 프로그래밍은 선언적 프로그래밍이라는 더 넓은 패러다임 중 한가지이다. 목표를 달성하는 과정을 기술하기보다 함수를 통해 추상화를 하며 추론하기 쉽게끔 프로그래밍을 하는 방식이다.

> [위키백과 - 선언형 프로그래밍](https://ko.wikipedia.org/wiki/%EC%84%A0%EC%96%B8%ED%98%95_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D)

# 불변성

데이터가 변경되지 않음을 의미한다. 무슨 소리인가 싶기도 하지만 원본 데이터는 바뀌지 않음을 의미한다. Array.concat, Array.map, Array.reduce 등을 통해 원본데이터를 변환한 데이터를 따로 만들어 사용한다.

# 순수함수

간단하게 말하자면 파라미터(인자)에 의해서만 반환값이 결정되는 함수이다. 또한 부수효과가 없는 함수이다.  
전역변수를 수정하거나 인자로 받은 객체를 수정하여 리턴하거나 하지 않는다.

# 고차함수

다름 함수를 조작할 수 있는 함수로 함수를 인자로 받거나 반환하는 기능을 지니고 있다.  
우리가 흔히 사용하는 Array.map, Array.filter, Array.reduce 등이 여기에 속한다.

# 커링

함수를 수행함에 있어 일부 인자를 먼저 받고 다른 인자를 나중에 받는 방식의 함수이다.

```js
const consoleLog = (userName) => (logMeassage) =>
  console.log(`${userName}: ${logMessage}`);
const setAredraMessage = consolLog("aredra");

setAredraMessage("Hello world!");
```

# 합성

작은 순수함수들을 합치는 과정이다. 흔히 체이닝을 많이 볼 수 있고, 아래와 같이 합성 함수를 사용하는 것도 방법이다.

```js
const compose =
  (...fns) =>
  (arg) =>
    fns.reduce((composed, f) => f(composed), arg);

const example = compose(function1, function2);

example("test");
```

# 참고 자료

> [Learning React](https://www.hanbit.co.kr/store/books/look.php?p_code=B3942115529)
