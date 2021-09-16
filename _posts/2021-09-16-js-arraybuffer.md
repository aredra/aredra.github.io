---
title: "JS - ArrayBuffer"
date: 2021-09-16
comments: true
read_time: false
toc: true
categories:
  - JS
tags:
  - arraybuffer
---

아직까지 사용해본적은 없지만 간간히 접해봤던 `ArrayBuffer` 에 대한 간략 메모

## ArrayBuffer

간단히 말하자면 바이너리 데이터를 저장하는 객체이다.  
JS에서 Array는 길이의 변환이 가능하지만 ArrayBuffer의 경우 생성한 인스턴스의 바이트 수를 변경할 수 없다는 특징을 지니고 있다.

❗ **인수로 받는 바이트의 수는 브라우저마다 범위가 다르다.**

```js
// 인자는 바이트 수를 나타낸다.
const buffer = new ArrayBuffer(10);
console.log(buffer); // ArrayBuffer(10)
```

## View

CRUD 를 나타낸다고 볼 수 있다. ArrayBuffer에 담긴 바이너리 데이터에 직접 수정 및 읽기가 불가능하여 View 를 통해 CRUD를 한다.  
ArrayBuffer 에 직접 View 는 할 수 없으며, TypedArray, DataView 로 사용한다. 이때 TypedArray로 펼치게 될 때 바이트를 나눌 수 있는 단위가 아니라면 펼칠 수 없다.

```js
const buffer = new ArrayBuffer(10);
const view = new Int16Array(buffer);
console.log(view); // Int16Array(5) [0, 0, 0, 0, 0]
```
