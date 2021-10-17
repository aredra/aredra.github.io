---
title: "JS - DataView"
date: 2021-10-17
comments: true
read_time: false
toc: true
categories:
  - JS
tags:
  - dataview
---

`ArrayBuffer` 를 view 하는 다른 방법인 `DataView` 객체에 대한 메모

## DataView?

`ArrayBuffer` 를 `view` 하기 위해 사용되는 객체이며, 메소드 이름으로 `get/set`, `타입`, `크기`를 구분할 수 있다. 그리고 `Endian` 사용이 가능하다!

> new DataView(ArrayBuffer instance[, offset byte, byteLength])

아래와 같이 생성할 수 있으며 `buffer`, `byteOffset`, `byteLength` 프로퍼티를 가지며,

- `buffer`: DataView 인스턴스를 생성할 때 넘겨준 ArrayBuffer
- `byteOffset`: offset 바이트, 2번째 인자값
- `byteLength`: view 바이트 수, 3번째 인자값

```js
const buffer = new ArrayBuffer(16);
const typedObj = new Int8Array(buffer);
typedObj.set([1, 2, 3, 4, 5]);

const dataViewObj = new DataView(buffer, 1, 2);
console.log(dataViewObj.getInt8(0)); // 2
console.log(dataViewObj.getInt8(1)); // 3
console.log(buffer);

console.log(dataViewObj.buffer === buffer);
console.log(dataViewObj.byteOffset);
console.log(dataViewObj.byteLength);
```

## 메소드

메소드 이름만 가지고 기능을 유추할 수 있다! 자세한 설명은 생략한다!  
👺 **TypedArray에 있는 Uint8C 타입이 없다.**

인자의 경우 `set`은 offsetByte, value 를 받고 `get`은 offsetByte 를 받는다.

- getInt8, getUint8
- getInt16, getUint16
- getInt32, getUint32, getFloat32
- getFloat64
- setInt8, setUint8
- setInt16, setUint16
- setInt32, setUint32, setFloat32
- setFloat64

```js
const buffer = new ArrayBuffer(5);
const typedObj = new Int8Array(buffer);
typedObj.set([1, 2, 3, 4, 5]);
const dataViewObj = new DataView(buffer, 1, 4);
dataViewObj.setInt8(0, 64);
dataViewObj.setInt8(1, 31);

console.log(dataViewObj.getInt8(0)); // 64
console.log(dataViewObj.getInt8(1)); // 31
console.log(dataViewObj.getInt8(2)); // 4
console.log(typedObj); // [1, 64, 31, 4, 5]
```

위와 같이 TypedArray 로 설정한 값을 같은 ArrayBuffer 인스턴스이므로 공유할 수 있다.

## Endian

바이트들의 값의 순서를 나타내는 용어로 들어는 보셨을 것이라고 생각하며, 자세한 내용은 위키백과를 참조!  
[엔디언 - 위키백과](https://ko.wikipedia.org/wiki/%EC%97%94%EB%94%94%EC%96%B8)

예를 들어 **1 2 3 4** 를 배열한다고 할 경우 구분법에 따라 아래와 같다.

- `Big-endian`: 앞에서부터 배치 **1 2 3 4**
- `Little-endian`: 뒤에서부터 배치 **4 3 2 1**
- `Mixed-endian`: 섞어서 배치 **2 1 4 3**

컴퓨터 OS나 CPU에 따라 다르며, JS 에서는 빅-에디언을 기본값을 가지고 있으며, **TypedArray 의 경우 지정할 수 있는 수단이 없으나 DataView에서는 지정할 수 있다.**

각 메소드의 마지막 인자로 지정이 가능한데 `true` 일 경우 리틀-엔디언 `false` 일 경우 빅-엔디언 이다. (기본값 `false`)

```js
const buffer = new ArrayBuffer(4);
const dataViewObj = new DataView(buffer);

dataViewObj.setInt16(1, 99, true);
console.log(dataViewObj.getInt16(1)); // 25344
console.log(dataViewObj.getInt16(1, true)); // 99
```

<hr/>

_개인이 참고하고자 작성한 글이며, 잘못된 정보가 있을 수 있습니다. 잘못된 정보는 메일로 보내주시면 감사하겠습니다._ 🙏
