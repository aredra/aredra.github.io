---
title: "JS template literal"
date: 2021-02-11
comments: true
read_time: false
categories:
  - JS
tags:
  - template literal
---

현재 JS에서는 **`**이라는 기호를 사용하여 문자열과 표현식을 사용한 문자열을 작성할 수 있다.  
이를 `Template Literal`이라고 하는데 이를 사용하는 함수 방법에 대해 정리하려 한다.

## Tagged Template

함수 이름과 같이 작성된 Template Literal 형태로 연결된 함수를 `Tag Function`이라고도 한다.
다음과 같이 사용이 가능하며, `String.raw()`함수와 비슷한 점을 볼 수 있다.

```js
const grade = "junior";
const age = "9999";
const intro = (text, grade, age) => {
  console.log(`${text[0]}${grade}${text[1]}${age}${text[2]}`);
};
intro`Aredra's ${grade} and ${age}.`;
```

표현식을 기준으로 split 되어 나오 값들이 배열형태로 1번째 파라미터로 들어가게 되며, 표현식의 경우 작성 순서대로 2번째 부터 순서대로 파라미터에 들어간다.
