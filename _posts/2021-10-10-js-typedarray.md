---
title: "JS - TypedArray"
date: 2021-10-10
comments: true
read_time: false
toc: true
categories:
  - JS
tags:
  - typedarray
---

_개인이 참고하고자 작성한 글이며, 잘못된 정보가 있을 수 있습니다. 잘못된 정보는 메일로 보내주시면 감사하겠습니다._ 🙏

<hr/>

저번에 잠깐 `ArrayBuffer` 에 대해 작성하면서 언급한 `TypedArray` 오브젝트에 정리글이다.  
이 역시 아직 사용해본 적이 없기는 하지만 알게되었으니 메모

## TypedArray?

아래의 9개의 오브젝트의 총칭으로, 상위 클래스로 9개의 오브젝트에 자동으로 상속되며 직접 사용이 불가능하다.
[TypedArray - 상세스펙](https://262.ecma-international.org/10.0/#sec-typedarray-objects)

|      생성자       |  타입   | 바이트 |
| :---------------: | :-----: | :----: |
|     Int8Array     |  Int8   |   1    |
|    Uint8Array     |  Uint8  |   1    |
| Uint8ClampedArray | Uint8C  |   1    |
|    Int16Array     |  Int16  |   2    |
|    Uint16Array    | Uint16  |   2    |
|    int32Array     |  Int32  |   4    |
|    Uint32Array    | Uint32  |   4    |
|   Float32Array    | Float32 |   4    |
|   Float64Array    | Float64 |   8    |

`TypedArray` 는 `Array-like` 형태로 `view` 를 하므로 `Array-like` 를 지원하는 메소드를 사용할 수 있다.
다른 타입들의 경우 최댓값을 넘게되면 값이 변동되는데 `Uint8C` 타입의 경우 최대값이 **유지**가 된다.

## 사용 예제

기본적인 생성자는 아래와 같이 작성할 수 있으며, `TypedArray` 는 필요한 타입으로 교체하여 작성하고, 1번째는 인자의 경우 그냥 원하는 크기만큼의 숫자를 넘겨줘도 된다.

> new TypedArray(ArrayBuffer instance[, offset byte, length])

복사받는 인스턴스의 바이트 수가 작으면 값이 잘린다. 앞에서부터 잘리게 되므로 값에 따라 복사되는 값이 다르게 된다.

```js
const int16Array = new Int16Array(4);
int16Array[0] = 123;
int16Array[1] = 12345;
int16Array[2] = 123456;
console.log(int16Array); // [123, 12345, -7616, 0]

const int8Array = new Int8Array(int16Array);
console.log(int8Array); // [123, 57, 64, 0]
```

파라미터에 `Array`, `Array-like` 를 넣을 수 있다. 그냥 오브젝트의 경우 생성은 되나 값이 할당되지 않는다.

```js
const test1 = new Int8Array([1, 2]);
console.log(test1); // [1, 2]

const test2 = new Int8Array({ 0: 1, 1: 2, length: 2 });
console.log(test2); // [1, 2]

const test3 = new Int8Array({ 0: 1, 1: 2 });
console.log(test3); // []
```

## TypedArray 프로퍼티

- `BYTES_PER_ELEMENT`: TypedArray 인스턴스에서 엘리먼트의 바이트 수를 반환
- `buffer`: TypedArray 인스턴스 생성에서 사용한 ArrayBuffer 를 반환
- `byteOffset`: ArrayBuffer 의 오프셋 값을 반환

```js
const buffer = new ArrayBuffer(10);
const test = new Int8Array(buffer, 3);

console.log(test.BYTES_PER_ELEMENT); // 1
console.log(test.buffer); // ArrayBuffer(10)
console.log(test.byteOffset); // 3
```

## 기타 함수, 메소드

자세한 내용은 직접 사용해보면 될 것 같다. ~~절대 귀찮아서가 아니다...~~ ☄️

- `from()`: new 연산자 없이 인스턴스를 생성할 수 있다.
- `of()`: new 연산자 없이 인스턴스를 생성할 수 있다. , 를 사용하여 구분
- `set()`: offset 만큼 떨어진 위치의 요소 파라미터의 값을 설정한다.
- `subarray()`: ArrayBuffer 의 데이터를 복사하여 반환
- `copyWithin()`: Array의 메소드와 같은 방법으로 첫번째 파라미터의 오브젝트를 복사 위의 subarray 와 형태가 조금 다르다.
