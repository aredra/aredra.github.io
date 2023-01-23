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

# 27. 배열

## 27.1 배열이란?

여러 개의 값을 순차적으로 나열한 자료구조로 자바스크립트뿐만 아니라 다른 프로그래밍언어에서도 사용빈도가 높은 기본적인 자료구조이다.

배열이 가지고 있는 값을 `요소` 라고 하며 배열의 요소는 자신의 위치를 나타내는 0 이상의 정수 `인덱스`를 가지며 이 인덱스를 통해서 배열의 요소에 접근할 수 있다. 또한 배열은 요소의 개수 `length 프로퍼티`를 갖는다.  
배열은 인덱스와 length 프로퍼티를 갖기 때문에 순차적으로 요소에 접근을 할 수 있다.

```js
const arr = ["apple", "banana", "oragne"];

console.log(arr[0]); // 0 번째 인덱스의 요소는 'apple' 이다.
console.log(arr.length); // 3

for (let i = 0; i < arr.length; i++) {
  console.log(arr[i]);
}
```

여기서 주의할 점은 자바스크립트에 배열이란 타입은 존재하지 않는다. 배열은 객체 타입이다. 하지만 일반 객체와는 구별되는 특징이 있다.  
일반적인 객체와 배열의 가장 명확한 차이는 `값의 순서`와 `length 프로퍼티`이다.

```js
const arr = ["apple", "banana", "oragne"];

console.log(typeof arr); // object
```

<table>
  <thead>
    <tr>
      <th>구분</th>
      <th>객체</th>
      <th>배열</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>구조</td>
      <td>프로퍼티 키와 프로퍼티 값</td>
      <td>인덱스와 요소</td>
    </tr>
    <tr>
      <td>값의 참조</td>
      <td>프로퍼티 키</td>
      <td>인덱스</td>
    </tr>
    <tr>
      <td>값의 순서</td>
      <td>X</td>
      <td>O</td>
    </tr>
    <tr>
      <td>length 프로퍼티</td>
      <td>X</td>
      <td>O</td>
    </tr>
  </tbody>
</table>

## 27.2 자바스크립트의 배열은 배열이 아니다.

자료구조에서 말하는 배열은 동일한 크기의 메모리 공간이 빈틈없이 연속적으로 나열된 자료구조를 말한다.  
각 요소는 하나의 데이터 타입으로 통일되어 있으며 연속적으로 인접해있고 이러한 배열을 `밀집 배열`이라 한다.  
이 밀집 배열의 경우 인덱스를 통해 단 한 번의 연산으로 임의의 요소에 접근 할 수 있다.

> 검색 대상 요소의 메모리 주소 = 배열 시작 메모리 주소 + 인덱스 \* 요소의 바이트 수

이 밀집 배열의 경우 인덱스를 통해 효율적으로 요소에 접근할 수 있다는 장점이 있지만 정렬되지 않은 특정요소를 검색하거나 요소를 삽입, 삭제 하는 경우 비용이 더 발생할 수 있는 단점도 있다.

자바스크립트의 배열은 위와 값이 일반적인 배열과 달리 각 요소는 값은 데이터타입이 아니며 연속적으로 이어지 있지 않을 수도 있다. 이와 같은 배열을 `희소 배열`이라 한다.  
이처럼 자바스크립트의 배열은 엄밀하게 말할 경우 일반적 의미의 배열이 아니며 이를 흉내 낸 `특수한 객체`이다.

```js
console.log(Object.getOwnPropertyDescriptors([1, 2, 3]));

/**
 0: {value: 1, writable: true, enumerable: true, configurable: true}
 1: {value: 2, writable: true, enumerable: true, configurable: true}
 2: {value: 3, writable: true, enumerable: true, configurable: true}
 length: {value: 3, writable: true, enumerable: false, configurable: false}
 [[Prototype]]: Object
 * /
```

자바스크립트 배열은 인덱스를 프로퍼티 키로 가지며, length 프로퍼티를 갖는 특수한 객체이다. 따라서 요소는 사실 `프로퍼티 값`이다.

- 일반적인 배열은 인데스로 요소에 빠르게 접근할 수 있다. 하지만 특정 요소를 검색하거나 요소를 삽입 삭제하는 경우 효율적이지 않다.
- 자바스크립트 배열은 해시 테이블로 구현된 객체이므로 인덱스로 요소에 접근하는 경우 일반적인 배열보다 성능적인 면에서 느릴 수밖에 없는 구조적인 단점이 있지만 특정 요소를 검색하거나 요소를 삽입, 삭제하는 경우 일반적인 배열보다 빠른 성능을 기대할 수 있다.

자바스크립트 엔진은 인덱스를 통한 배열 요소 접근 속도을 보완하기 위해 배열을 일반객체와 구별하여 좀 더 배열처럼 동작하도록 최적화하여 구현하였다. 성능을 비교할 경우 유의미한 차이가 있음을 확인할 수 있다.

```js
const arr = [];

console.time("Array Performance Test");
for (let i = 0; i < 100000000; i++) {
  arr[i] = i;
}
console.timeEnd("Array Performance Test");
// Array Performance Test: 155.698974609375 ms

const obj = {};

console.time("Object Performance Test");
for (let i = 0; i < 100000000; i++) {
  obj[i] = i;
}
console.timeEnd("Object Performance Test");
// Object Performance Test: 277.626953125 ms
```

## 27.3 length 프로퍼티와 희소 배열

length 프로퍼티는 요소의 개수를 나타내고 0 ~ 2^32 - 1(4,294,967,295) 사이의 정수값을 가지며 빈 배열일 경우 0 이다.  
length 프로퍼티 값은 배열의 요소를 추가하거나 삭제되면 자동으로 갱신된다. 배열의 길이를 바탕으로 결정되지만 임의의 숫자 값을 명시적으로 할당할 수 있는데 실제 길이보다 작을 경우 배열의 길이가 줄어 들고 클 경우 값은 변경되지만 실제로 배열이 늘어나지는 않는다.

```js
const arr = [1, 2, 3];
console.log(arr.length); // 3

arr.push(4);
console.log(arr.length); // 4

arr.pop();
console.log(arr.length); // 3

arr.length = 2;
console.log(arr); // [1, 2]

arr.length = 5;
console.log(arr); //  [1, 2, empty × 3]
```

length 프로퍼티를 임의로 실제 배열의 길이보다 큰 값을 할당할 경우 위와 같이 비어있는 요소가 있음을 볼 수 있지만 해당 요소에 대해서 메모리 공간을 확보하지 않고 생성하지도 않는다. 이처럼 배열의 요소가 연속적으로 위치하지 않고 일부가 비어 있는 요소를 희소 배열이라고 하며, 자바스크립트는 희소 배열을 문법적으로 허용한다.

```js
const sparse = [, 2, , 4];
console.log(sparse.length); // 4

console.log(Object.getOwnPropertyDescriptors(sparse));
/*
1: {value: 2, writable: true, enumerable: true, configurable: true}
3: {value: 4, writable: true, enumerable: true, configurable: true}
length: {value: 4, writable: true, enumerable: false, configurable: false}
*/
```

희소배열은 length 와 배열 요소의 개수가 일치하지 않고 실제 요소 개수보다 언제나 크다.  
자바스크립트는 문법적으로 희소배열을 허용하지만 이는 사용하지 않는 것을 권장한다. 배열의 기본적인 개념과 맞지 않으며 성능에도 좋지 않은 영향을 주기 때문이다. ~~모던 자바스크립트 엔진은 요소의 타입이 일치하는 배열을 생성할 때 연속된 메모리 공간을 확보하는 것으로 알려져 있다.~~  
**배열에는 같은 타입의요소를 연속적으로 위치시키는 것이 좋다.**

## 27.4 배열 생성

### 27.4.1 배열 리터럴

배열 생성에는 다양한 방식이 있지만 가장 일반적인 방법은 배열 리터럴을 사용하는 것이다.

```js
let arr = [1, 2, 3];
console.log(arr);

arr = [];
console.log(arr.length); // 0

arr = [1, , 3];
console.log(arr); // 희소배열
```

### 27.4.2 Array 생성자 함수

Array 생성자 함수를 통해 생성할 수 있다.

> 🚨&nbsp;이 경우 전달되는 인수의 개수에 따라 다르게 동작하므로 주의가 필요

- 전달된 인수가 1개이고 숫자인 경우 length 프로퍼티 값이 인수인 배열을 생성

```js
// 인수로 배열의 길의 범위 내의 정수값을 입력해야 한다. 벗어날 경우 에러 발생
const arr = new Array(10);

console.log(arr); // [empty * 10]
console.log(arr.length); // 10

new Array(999999999999999); // Uncaught RangeError: Invalid array length
new Array(-1); // Uncaught RangeError: Invalid array length
```

- 전달된 인수가 없는 경우 빈 배열을 생성

```js
const arr = new Array(); // []
```

- 전달된 인수가 2개 이상이거나 숫자가 아닌 경우 인수를 요소로 갖는 배열을 생성

```js
let arr = new Array(1, 2, 3);
console.log(arr); // [1, 2, 3]

arr = new Array({});
console.log(arr); // [{}]
```

**Array 는 생성자 함수로 호출하지 않고 일반 함수로 호출해도 배열을 생성하는 생성자 함수가 동작한다!**

### 27.4.3 Array.of

ES6에 도입된 메서드로 전달된 인수를 요소로 갖는 배열을 생성한다.

```js
Array.of(1); // [1]
Array.of(2, "te", 3); // [2, 'te', 3]
Array.of("string"); // ['string']
```

### 27.4.4 Array.from

ES6에서 도입된 메서드로 유사 배열 객체 또는 이터러블 객체를 인수로 전달 받아 배열로 변환하여 반환한다.  
두 번째 인수로 콜백 함수를 전달하여 값을 만들면서 요소를 채울 수 있다. 콜백 함수는 첫 번째로 생성된 배열의 요소 값과 인덱스를 매개변수로 갖는다.

```js
// 유사 배열 객체
Array.from({ length: 3, 0: 0, 1: 1 }); // [0, 1, undefined]

// 이터러블 - ch.34 에서 설명
Array.from("hello"); // ['h', 'e', 'l', 'l', 'o']

Array.from({ length: 3 }, (_, i) => i); // [0, 1, 2]
```

## 27.5 배열 요소의 참조

배열의 요소를 참조할 때는 대괄호 표기법을 사용하고 안에는 인덱스가 와야한다. 존재하지 않는 요소에 접근할 경우 undefined 가 반환된다.  
희소 배열의 존재하지 않는 요소를 참조해도 undefined 가 반환된다.
**배열은 인덱스를 나타내는 문자열을 프로퍼티 키로 갖는 객체이다**

```js
const arr = [0, 1, 2];

console.log(arr[0]); // 1
console.log(arr[1]); // 2
console.log(arr[4]); // undefined
```

## 27.6 배열 요소의 추가와 갱신

객체에 프로퍼티를 동적으로 추가할 수 있는 것처럼 배열에도 요소를 동적으로 추가할 수 있다. 존재하지 않는 인덱스를 사용해 값을 할당하면 새로운 요소가 추가된다. 이 때 length 프로퍼티 값은 자동 갱신된다.  
현재 배열의 length 프로퍼티 값보다 큰 인덱스에 값을 넣을 경우 희소 배열이 된다.

> 🚨&nbsp; 인덱스는 요소의 위치를 나타내므로 범위내의 0 이상의 정수(문자열 형태 가능)를 사용해야 한다. 이외의 값을 사용할 경우 요소가 생성되는 것이 아니라 프로퍼티가 생성되며 length 프로퍼티 값에 영향을 주지 않는다.

```js
const arr = [0];
arr[1] = 1;

console.log(arr); // [0, 1]
console.log(arr.length); // 2

arr[100] = 100;
console.log(arr); // [0, 1, empty × 98, 100]
console.log(arr.length); // 101

arr["2"] = 3;
arr[1.1] = 3;
arr.bar = 4;

console.log(arr); // [0, 1, 3, empty × 97, 100, 1.1: 3, bar: 4]
```

## 27.7 배열 요소의 삭제

배열은 사실 객체이기 때문에 delete 연산자를 사용할 수 있지만 이는 희소배열이 되며 length 프로퍼티 값은 변하지 않기 때문에 사용하지 앟는 것이 좋으며 splice 메서드를 활용하자.

> Array.prototype.splice(삭제를 시작할 인덱스, 삭제할 요소 수)

```js
const arr = [1, 2, 3];

delete arr[1];
console.log(arr); // [1, empty, 3]
console.log(arr.length); // 3

arr.splice(1, 1);
console.log(arr); // [1, 3]
console.log(arr.length); // 2
```

## 27.8 배열 메서드

자바스크립트는 배열을 다루는 다양한 빌트인 메서드를 제공한다.  
**사용하기 앞서 원본 배열을 직접 변경하는 메서드와 원본 배열을 직접 변경하지 않고 새로운 배열을 생성하여 반환하는 메서드 종류를 주의하자**

원본 배열을 직접 변경하는 메서드는 부수 효과가 있을 수 있기 때문에 사용에 주의하고 가급적 원본 배열을 직접 변경하지 않는 메서드를 사용하는 편이 좋다.

### 27.8.1 Array.isArray

전달 받은 인수가 배열인지를 확인하는 정적 메서드이다.

```js
// true
Array.isArray([]);
Array.isArray(new Array());

// false
Array.isArray("string");
Array.isArray({});
Array.isArray({ length: 1, 0: 1 });
```

### 27.8.2 Array.prototype.indexOf

원본 배열에서 인수로 전달된 요소를 검색하여 인덱스를 반환한다.

- 원본 배열에 인수로 전달한 요소와 중복되는 요소가 여러 개 있다면 첫 번째 요소의 인덱스르 반환
- 원본 배열에 인수로 전달한 요소가 존재하지 않으면 -1 을 반환

```js
const arr = [1, 2, 2, 3, 4];

arr.indexOf(2); // 1
arr.indexOf(5); // -1
// 두 번째 인덱스는 검색을 시작하는 인덱스
arr.indexOf(2, 2); // 2
```

예전에는 배열에 해당하는 요소가 있는지 확인하는 용도로 많이 사용하였으나 이제는 ES7에 추가된 includes 메서드를 사용하는 것이 가독성이 더 좋다.

### 27.8.3 Array.prototype.push

인수로 전달받은 모든 값을 원본 배열의 마지막 요소로 추가하고 변경된 length 프로퍼티 값을 반환한다.  
하지만 push 메서드는 성능 면에서 좋지 않고 length 프로퍼티를 이용한 방법이 더 빠르며 원본 배열을 수정하기 때문에 부수효과가 있어 스프레드 문법을 활용하는 편을 권장하고 있다.

```js
const arr = [1, 2];

console.log(arr.push(3, 4)); // 4
console.log(arr); // [1, 2, 3, 4]

// arr[arr.length] = 3;
const newArr = [...arr, 3, 4];
console.log(newArr); // [1, 2, 3, 4, 3, 4]
```

### 27.8.4 Array.prototype.pop

pop 메서드는 원본 배열에서 마지막 요소를 제거하고 제거한 요소를 반환한다. 빈 배열일 경우 undefined 를 반환한다.

```js
const arr = [1, 2];

console.log(arr.pop()); // 2
console.log(arr); // [1]
```

push, pop 두 메서드를 사용하면 스택을 쉽게 구현할 수 있다.

> 💿&nbsp; Stack: 후입 선출(LIFO) 방식의 자료구조

```js
class Stack {
  #array;

  constructor(array = []) {
    if (!Array.isArray(array)) {
      throw new Error(`${array} is not an array`);
    }
    this.#array = array;
  }

  push(value) {
    return this.#array.push(value);
  }
  pop() {
    return this.#array.pop();
  }
  entries() {
    return [...this.#array];
  }
}

const stack = new Stack([1, 2]);
console.log(stack.entries()); // [1, 2]

stack.push(3);
console.log(stack.entries()); // [1, 2, 3]

stack.pop();
console.log(stack.entries()); // [1, 2]
```

### 27.8.5 Array.prototype.unshift

unshift 메서드는 인수로 전달받은 모든 값을 원본 배열의 선두에 요소로 추가하고 length 프로퍼티 값을 반환한다.  
push 메서드와 마찬가지로 부수효과가 있어 스프레드 문법 사용을 권장하고 있다.

```js
const arr = [1, 2];
console.log(arr.unshift(0)); // 3
console.log(arr); // [0, 1, 2]

console.log([0, ...arr]); // [0, 0, 1, 2]
```

### 27.8.6 Array.prototype.shift

shift 메서드는 원본 배열에서 첫 번째 요소를 제거하고 해당 요소를 반환한다. 빈 배열일 경우 undefined 를 반환한다.

```js
const arr = [1, 2];

console.log(arr.shift()); // 1
console.log(arr); // [2]
```

shift 메서드와 push 메서드를 사용하면 큐를 쉽게 구현할 수 있다.

> 💿&nbsp; Queue: 선입 선출(FIFO) 방식의 자료구조

```js
class Queue {
  #array;

  constructor(array = []) {
    if (!Array.isArray(array)) {
      throw new Error(`${array} is not an array`);
    }
    this.#array = array;
  }

  enqueue(value) {
    return this.#array.push(value);
  }
  dequeue() {
    return this.#array.shift();
  }
  entries() {
    return [...this.#array];
  }
}

const queue = new Queue([1, 2]);
console.log(queue.entries()); // [1, 2]

queue.enqueue(3);
console.log(queue.entries()); // [1, 2, 3]

queue.dequeue();
console.log(queue.entries()); // [1, 2]
```

### 27.8.7 Array.prototype.concat

concat 메서드는 인수로 전달된 값들을 원본 배열의 마지막 요소로 추가한 **새로운 배열**을 반환한다.  
인수로 전달한 값이 배열인 경우 배열을 해체하여 새로운 배열을 요소로 추가한다.

```js
const arr1 = [1, 2];
const arr2 = [3, 4];

const result = arr1.concat(arr2);
console.log(result); // [1, 2, 3, 4]
console.log(arr1); // [1, 2]
```

push 와 unshift 메서드는 concat 메서드로 대체할 수 있다. 다음과 같은 차이가 있다.

- concat 메서드는 원본 배열을 변경하지 않는다.
- 인수로 전달받은 값이 배열인 경우 concat은 1뎁스만 배열을 해체하여 마지막요소에 추가한다.

```js
const arr1 = [1, 2];
const arr2 = [
  [3, 4],
  [5, 6],
];

const result = arr1.concat(arr2);
console.log(result); // [1, 2, [3, 4], [5, 6]]
console.log(...arr1, ...arr2); // [1, 2, [3, 4], [5, 6]]
```

사실 해당 메서드도 스프레드 문법을 통해 대체할 수 있고 이를 권장한다.

### 27.8.8 Array.prototype.splice

원본 배열의 값을 직접 변경하는 메서드로 중간 요소를 추가하거나 제거할 경우 사용한다.

> 💿&nbsp; Array.prototype.splice(startIndex[, deleteCount, itmes])
>
> - startIndex: 변경을 시작한 인덱스, 음수일 경우 끝에서부터를 의미
> - delecteCount: 제거할 요소 개수
> - items: 제거한 위치에 삽일할 요소들 목록

```js
const arr1 = [1, 2, 3, 4, 5];

const result = arr1.splice(1, 2, 20, 30);
console.log(result); // [2, 3]
console.log(arr1); // [1, 20, 30, 4, 5]

// 시작 인덱스만 지정할 경우 끝까지 삭제한다.
arr1.splice(3);
console.log(arr1); // [1, 20, 30]

// 책에 다양한 예제가 있으나 생략함
```

### 27.8.9 Array.prototype.slice

slice 메서드는 인수로 전달된 범위의 요소들을 복사하여 배열로 반환한다. 원본 배열은 변경되지 않는다. 두 개의 매개변수를 갖는다.

- start: 복사를 시작할 인덱스이다. 음수의 경우는 끝에서부터의 인덱스
- end: 복사를 종료할 인덱스로 생략가능하며 기본값은 lenght 값이다.

> 🚨 &nbsp; 이 때의 복사는 얕은 복사이다.

```js
const arr1 = [1, 2, 3, 4, 5];

console.log(arr1.slice(1, 3)); // [2, 3]
console.log(arr1); // [1, 2, 3, 4, 5]

console.log(arr1.slice(-1, -4)); // [5]
// 인수를 모두 생략하면 원본 배열의 얕은 복사본이 생김
console.log(arr1.slice()); // [1, 2, 3, 4, 5]

// 유사객체, 이터러블을 배열로 변환하는 예제 생략
```

### 27.8.10 Array.prototype.join

인수로 전달받은 문자열(구분자)로 요소를 연결하여 문자열로 반환한다. 기본 구분자는 ',' 이다.

```js
const arr1 = [1, 2, 3, 4, 5];
console.log(arr1.join()); // 1,2,3,4,5
```

### 27.8.11 Array.prototype.reverse

원본 배열의 순서를 변경하여 원본 배열이 변경된다. 변경된 원본 배열이 반환된다.

```js
const arr1 = [1, 2, 3, 4, 5];
const result = arr1.reverse();
console.log(result); // [5, 4, 3, 2, 1]
console.log(arr1 === result); // true
```

### 27.8.12 Array.prototype.fill

ES6에 도입된 fill 메서드는 인수로 전달받은 값을 배열의 처음부터 끝까지 요소로 채우고 이때 원본 배열이 변경된다.

- value: 첫번째 인수로 채울 값이다.
- startIndex: 해당 값으로 채울 시작 인덱스
- endIndex: 요소 채우기를 멈출 인덱스

```js
const arr1 = [1, 2, 3, 4, 5];
arr1.fill(0); // [0, 0, 0, 0, 0]
arr1.fill(1, 1); // [0, 1, 1, 1, 1]
arr1.fill(2, 2, 3); // [0, 1, 2, 1, 1]
```

### 27.8.13 Array.prototype.includes

ES7 에서 도입된 메서드로 배열 내 특저 요소가 포함되어있는 여부를 확인
과거에는 indexOf 를 많이 사용했으나 -1 로 한번 더 비교하는 가독성인 부분과 NaN 포함 여부를 알 수 없다는 문제점이 있다.

```js
const arr1 = [1, 2, 3, 4, NaN];
console.log(arr1.includes(1)); // true
console.log(arr1.includes(NaN)); // true
console.log(arr1.indexOf(NaN)); // -1
```

### 27.8.14 Array.prototype.flat

ES10에 도입된 메서드로 인수로 전달한 깊이만큼 재귀적으로 배열을 평탄화한다.  
기본값은 1이다.

```js
[1, 2, [3, 4]].flat(); // [1, 2, 3, 4]
[1, [2, [3, 4]]].flat(); // [1, 2, [3, 4]]
[1, [2, [3, 4]]].flat(2); // [1, 2, 3, 4]
[1, [2, [3, 4]]].flat(Infinity); // [1, 2, 3, 4]
```

## 27.9 배열 고차 함수

고차 함수는 함수를 인자로 받거나 함수를 반환하는 함수를 지칭힌다. 일반적으로 고차함수는 가변 데이터를 피하고 불변성을 지향하는 함수형 프로그래밍에 기반을 두고 있다.  
순수 함수를 통해 부수 효과를 최대한 억제하여 오류를 피하고 프로그램의 안정성을 높이려는 노력의 일환이라고 할 수 있다.  
자바스크립트에서는 배열의 다양한 고차 함수를 제공하는데 활용도가 매우 높다. 작성자 기준 익숙한 함수들의 경우 생략이 많을 수 있다.

### 27.9.1 Array.prototype.sort

배열의 요소를 정렬하여 원본 배열을 직접 변경한다. 기본적으로 오름차순이다.  
문자열의 비교의 경우 별문제 없으나 숫자의 경우는 문제가 생긴다. sort 메서드의 기본 정렬 순서는 유니코드 코드 포인트의 순서를 따르며 숫자 역시 문자열로 변환후 정렬하므로 100 이 20 보다 먼저 정렬되는 경우를 볼 수 있다.

```js
const arr1 = ["a", "c", "e", 10, 2, "100", 40];
console.log(arr1.sort()); // [10, '100', 2, 40, 'a', 'c', 'e']
console.log(arr1.sort((a, b) => a - b)); // [2, 10, 40, '100', 'a', 'c', 'e']
```

이럴 경우 정렬 순서를 정의하는 비교 함수를 인수로 전달하면 되는데 반환값에 따라 아래와 같다.

- 반환값 음수: 첫 번째 인자 우선 정렬
- 반환값 0: 정렬 안함(자바스크립트 엔진마다 다를 수 있음)
- 반환값 양수: 두 번째 인자 우선 정렬

> 💿&nbsp; 해당 메서드는 quick sort 를 썼으나 ES10 에서 timesort 로 변경

### 27.9.2 Array.prototype.forEach

for 문을 대체할 수 있는 고차 함수다. 해당 메서드는 원본 배열을 변경하지 않지만 콜백함수를 통해서 원본 함수를 변경할 수 있다.  
반환값은 언제나 undefined 이다.

```js
// 요소값, 인덱스, this 를 인자로 받는다.
const result = [1, 2, 3].forEach((item, index, arr) => {
  if (index == 0) {
    continue; // SyntaxError
  }
  if (index == 1) {
    break; // SyntaxError
  }
});
console.log(result); // undefined
```

이 메서드는 for 문 과 달리 break, continue 를 할 수 없고 성능이 좋지도 않지만 요소가 대단히 많은 경우가 아니면 가독성이 더 좋은 해당 메서드 사용을 권장한다.

### 27.9.3 Array.prototype.map

호출한 배열의 모든 요소를 순회하면서 인수로 전달 받은 콜백함수를 반복 호출한다.  
콜백 함수의 반환값들로 구성된 새로운 배열을 반환하며, 원본 배열을 변경되지 않는다.  
forEach 와 같이 콜백함수의 인수는 3개이며, 순서 역시 동일하다.

```js
const arr = [1, 4, 9, 16];
const roots = arr.map((num) => Math.sqrt(num));

console.log(arr); // [1, 4, 9, 16]
console.log(roots); // [1, 2, 3, 4]
```

### 27.9.4 Array.prototype.filter

호출한 배열의 모든 요소를 순회하며 인수로 전달받은 콜백 함수의 반환값이 true 요소로만 구성된 새로운 배열을 반환한다. 원본 배열은 변경되지 않는다.  
콜백 함수의 매개변수는 동일하게 3가지 이다.

```js
const num = [1, 2, 3, 4, 5, 6];
const odds = num.filter((item) => item % 2);
console.log(num); // [1, 2, 3, 4, 5, 6]
console.log(odds); // [1, 3, 5]
```

### 27.9.5 Array.prototype.reduce

자신을 호출한 배열의 모든 요소를 순회하며 인수로 전달 받은 콜백 함수를 반복호출하고 반환값을 계속 다음 순회 때 넘겨주는 함수이다.  
첫 번째 인자로 콜백함수, 두 번째 인자로 초기값을 넘겨줘야 한다.

```js
const num = [1, 2, 3, 4, 5, 6];
const sum = num.reduce((acc, el, _index, _arr) => acc + el, 0);
console.log(num); // [1, 2, 3, 4, 5, 6]
console.log(sum); // 21
```

해당 메서드는 호출할 때 언제나 초기값을 전달하는 것이 오류를 방지하고 안전하다.

### 27.9.6 Array.prototype.some

배열의 요소를 순회하면서 인수로 전달된 콜백 함수를 호출하고 한 번이라도 참이면 true, 모두 거짓일 경우 false 를 리턴한다.  
빈 배열인 경우 언제나 false 를 리턴한다. true 를 반환하면 순회를 종료할 수 있다.

```js
const num = [1, 2, 3, 4, 5, 6];
num.some((num, index) => {
  console.log(num);
  return index > 2;
});
```

### 27.9.7 Array.prototype.every

배열의 요소를 순회하면서 인수로 전달된 콜백 함수를 호출하고 한 번이라도 모두 참이면 true, 하나라도 거짓일 경우 false 를 리턴한다.  
빈 배열인 경우 언제나 true 를 리턴한다. false 를 반환하면 순회를 종료할 수 있다.

```js
const num = [1, 2, 3, 4, 5, 6];
num.every((num, index) => {
  console.log(num);
  return index < 2;
});
```

### 27.9.8 Array.prototype.find

ES6에 도입된 메서드로 순회하면서 콜백함수의 반환값이 true 인 첫 번째 요소를 반환한다.  
없을 경우 undefined 를 반환한다.

```js
const users = [
  { id: 1, name: "Jonah1" },
  { id: 2, name: "Jonah2" },
  { id: 3, name: "Jonah3" },
  { id: 4, name: "Jonah4" },
  { id: 5, name: "Jonah5" },
];

users.find((user) => user.id === 3);
```

### 27.9.9 Array.prototype.findIndex

indexOf 메서드를 콜백함수로 인자 받는 버전, find 메서드에서 요소값이 아닌 인덱스를 리턴해주는 메서드이다.

```js
const users = [
  { id: 1, name: "Jonah1" },
  { id: 2, name: "Jonah2" },
  { id: 3, name: "Jonah3" },
  { id: 4, name: "Jonah4" },
  { id: 5, name: "Jonah5" },
];

users.find((user) => user.id === 3); // 2
```

### 27.9.10 Array.prototype.flatMap

ES10 에서 도입된 메서드로 map 메서드로 생성된 새로운 배열을 평탄화한다.  
map 과 flat 을 순차적으로 실행하는 효과가 있으나 flat 메서드처럼 깊이를 인수로 지정할 수 있어 해당 기능이 필요한 경우 순차적으로 실행할 필요가 있다.

```js
const arr = ["hello", "world"];
arr.flatMap((str, index) => [index, [str, str.length]]);
// [0, Array(2), 1, Array(2)]
arr.map((str, index) => [index, [str, str.length]]).flat(2);
// [0, 'hello', 5, 1, 'world', 5]
```

# 28. Number

표준 빌트인 객체인 Number 는 원시 타입인 숫자를 다룰 때 유용한 프로퍼티와 메서드를 제공한다.

## 28.1 Number 생성자 함수

표준 빌트인 객체인 Number 객체는 생성자 함수 객체다. 따라서 new 연산자와 함께 호출하여 인스턴스를 생성할 수 있고, 인수를 할당하지 않을 경우 [[NumberData]] (`[[PrimitiveValue]]`) 내부 슬롯에 0을 할당한 래퍼 객체를 생성한다.  
인수에 숫자가 아닌 값이 들어오면 변환 후 넣으며 숫자가 될 수 없다면 NaN 을 내부 슬롯에 할당하여 객체를 생성한다.  
앞서 다뤘지만 new 를 사용하지 않고 Number 생성자함수를 호출하면 인스턴스가 아닌 숫자로 반환한다.

```js
const num1 = new Number();
const num2 = new Number("1");
const num3 = new Number("aefafe");

console.log(num1); // Number {0}
console.log(num2); // Number {1}
console.log(num3); // Number {NaN}
```

## 28.2 Number 프로퍼티

### 28.2.1 Number.EPSILON

ES6에 도입되었으며 1과 1보다 큰 숫자 중에서 가장 작은 숫자와의 차이
부동소수점 산술연산에서 발생하는 오차를 해결하기 위해서 사용한다.

```js
0.1 + 0.2 !== 0.3;

function isEqual(a, b) {
  // a 와 b 의 차이의 절대값이 입실론보다 작으면 같은 수로 인정한다.
  return Math.abs(a - b) < Number.EPSILON;
}
```

### 28.2.2 Number.MAX_VALUE

자바스크립에서 표현할 수 있는 가장 큰 양수값 더 큰수로 Infinity가 있다.

```js
Number.MAX_VALUE;
```

### 28.2.3 Number.MIN_VALUE

자바스크립트에서 표현할 수 있는 가장 작은 양수값 바로 아래 0 이 있다.

```js
Number.MIN_VALUE;
```

### 28.2.4 Number.MAX_SAFE_INTEGER

자바스크립트에서 안전하게 표현할 수 있는 가장 큰 정수값

```js
Number.MAX_SAFE_INTEGER;
```

### 28.2.5 Number.MIN_SAFE_INTEGER

자바스크립트에서 안전하게 표현할 수 있는 가장 작은 정수값

```js
Number.MIN_SAFE_INTEGER;
```

### 28.2.6 Number.POSITIVE_INFINITY

양의 무한대

```js
Number.POSITIVE_INFINITY;
```

### 28.2.7 Number.NEGATIVE_INFINITY

음의 무한대

```js
Number.NEGATIVE_INFINITY;
```

### 28.2.8 Number.NaN

숫자가 아님을 의미. Number.NaN 과 window.NaN 은 같다.

```js
Number.NaN;
```

## 28.3 Number 메서드

### 28.3.1 Number.isFinite

ES6에서 도입된 메서드로 인수로 전달된 값이 유한수인지 여부를 확인  
인수가 NaN 일 경우 언제나 false 를 반환하고 빌트인 전역함수 isFinite 와 다르게 인수에 대해서 암묵적 타입변환을 하지 않는다.

```js
Number.isFinite(Number.MAX_VALUE); // true

Number.isFinite(Infinity); // false
Number.isFinite(NaN); // false
Number.isFinite(null); // fasle
isFinite(null); // true
```

### 28.3.2 Number.isInteger

ES6에 도입된 메서드로 인수로 전달된 값이 정수인지 검사하여 결과를 반환한다. 암묵적 타입 변환을 하지 않는다.

```js
Number.isInteger(0); // true
Number.isInteger("0"); // false
```

### 28.3.3 Number.isNaN

ES6에 도입된 메서드로 인수로 전달된 값이 NaN 인지 여부를 확인한다. 빌트인 isNaN 과 인수로 받은 값에 대해서 암묵적 타입 변환을 하는지에 따른 차이가 있다.

```js
Number.isNaN(NaN); // true
Number.isNaN(undefined); // false
isNaN(undefined); // true
```

### 28.3.4 Number.isSafeInteger

ES6에서 도입된 인수로 전달된 숫자값이 안전한 정수인지 검사한다.
안전한 정수는 -(2^53 - 1) ~ (2^53 - 1) 사이의 값이다.

```js
Number.isSafeInteger(0); // true
Number.isSafeInteger("0"); // false
```

### 28.3.5 Number.prototype.toExponential

숫자를 지수 표기법으로 변환하여 문자열로 반환한다. 인수로 소수점 이하로 표현할 자리수를 전달할 수 있다.

```js
(77.1234).toExponential(); // '7.71234e+1'
(77.1234).toExponential(1); // '7.7e+1'

77.toExponential(); //SyntaxError: Invalid or unexpected token
77.1234.toExponential();
77 .toExponential();
```

숫자 리터럴로 할 경우 위와 같이 에러가 나는데 숫자 뒤 . 은 의미가 모호하기 때문이다.  
이미 소수점이 등장하였거나 공백을 먼저 표기하여 사용이 가능하다.

### 28.3.6 Number.prototype.toFixed

숫자를 반올림하여 문자열로 반환하며 인수로 소수점을 표현하고 0~20 까지 가능하다. 책에는 적혀있으나 자바스크립트 엔진에 따라 다른것으로 보인다. 기본값 0

```js
(12345.6789).toFixed(); // '12346'
(12345.6789).toFixed(2); // '12345.68'
(12345.6789).toFixed(-1);
// RangeError: toFixed() digits argument must be between 0 and 100
(12345.6789).toFixed(21);
```

### 28.3.7 Number.prototype.toPrecision

인수로 전달받은 전체 자릿수까지 유효하도록 나머지 자릿수를 반올림하여 문자열로 반환한다. 인수로 전달 받은 전체 자리수로 표기 못할 경우 지수 표기법을 사용한다.  
0~21 사이의 정수값이라고 하는데 이 역시 자바스크립트 엔진에 따라 다른 듯 보인다. 기본값은 0이다.

```js
(12345.6789).toPrecision(); // '12345.6789'
(12345.6789).toPrecision(2); // '1.2e+4'
(12345.6789).toPrecision(-1);
// RangeError: toPrecision() digits argument must be between 0 and 100
(12345.6789).toPrecision(21);
// '12345.6789000000007945'
```

### 28.3.8 Number.prototype.toString

숫자를 문자열로 변환하는 메서드. 인수로 2 ~ 36 정수값을 받아 해당하는 진법으로 반환한다. 기본값은 10이다.

```js
(10).toString(); // '10'
(10).toString(2); // '1010'
(36).toString(36); // 'z'
(10).toString(37); // RangeError: toString() radix argument must be between 2 and 36
```

# 29. Math

표준 빌트인 객체인 Math는 수학적인 상수와 함수를 위한 프로퍼티와 메서드를 제공하고 Math는 생성자 함수가 아니다. 따라서 정적 프로퍼티와 정적 메서드만 제공한다.

## 29.1 Math 프로퍼티

### 29.1.1 Math.PI

원주율

```js
Math.PI;
```

## 29.2 Math 메서드

### 29.2.1 Math.abs

절대값 반환, 양수 또는 0, NaN 이 반환될 수 있다. 인수를 암묵적 타입 변환하는 경우가 좀 달라서 주의가 필요하다.

```js
Math.abs(-1); // 1
Math.abs([]); // 0
Math.abs(null); // 0
Math.abs(""); // 0
Math.abs(undefined); // NaN
Math.abs({}); // NaN
Math.abs("string"); // NaN
Math.abs(); // NaN
```

### 29.2.2 Math.round

인수로 전달된 숫자의 소수점 이하를 반올림한 정수 반환

```js
Math.round(1.4); // 1
Math.round(1.586); // 2
```

### 29.2.3 Math.ceil

인수로 전달된 숫자의 소수점 이하를 올림한 정수 반환

```js
Math.ceil(1.03); // 2
Math.ceil(-1.43); // -1
```

### 29.2.4 Math.floor

인수로 전달된 숫자의 소주점 이하를 버림한 정수 반환

```js
Math.floor(2.93); // 2
Math.floor(-1.43); // -2
```

### 29.2.5 Math.sqrt

인수로 전달된 숫자의 제곱근 반환

```js
Math.sqrt(-9); // NaN
Math.sqrt(5); // 2.23606797749979
```

### 29.2.6 Math.random

0 ~ 1 미만의 임의의 난수를 반환한다.

```js
// 1 에서 10 미만의 랜덤한 정수를 구하고 싶을 경우
const random = Math.floor(Math.random() * 10 + 1);
console.log(random);
```

### 29.2.7 Math.pow

첫번째 인수를 밑으로 두번째 인수를 지수로 거듭제곱한 결과를 반환한다.  
ES7에 도입된 지수 연산자 \*\* 가 가독성이 더 좋아서 권장된다.

```js
Math.pow(2, 4); // 16
Math.pow(2, -1); // 0.5
Math.pow(2); // NaN

2 ** 4; // 16
```

### 29.2.8 Math.max

인수 중 가장 큰 수를 반환한다. 인수가 없을 경우 -Infinity 가 반환된다.
배열의 경우는 스프레드를나 apply 메서드를 활용해야한다.

```js
Math.max(3, 1); // 3
Math.max(...[53, 43, 23]); // 53
Math.max.apply(null, [53, 43, 23]); // 53
```

### 29.2.9 Math.min

인수 중 가장 작은 수를 반환한다. 인수가 없을 경우 Infinity 가 반환된다.
배열의 경우는 스프레드를나 apply 메서드를 활용해야한다.

```js
Math.min(3, 1); // 1
Math.min(...[53, 43, 23]); // 23
Math.min.apply(null, [53, 43, 23]); // 23
```

# 30. Date

표준 빌트인 객체인 Date 는 날짜와 시간을 위한 빌트인 객체이면서 생성자 함수이다.  
UTC 는 국제 표준시를 말하고 UTC 는 GMT 와 초의 소수점만 차이나기 때문에 일상에서는 혼용되어 불리기도 하지만 기술적 표기는 UTC 로 한다.  
KST 는 UTC 에 9 시간을 더한 시간이다.  
자바스크립트가 실행된 시스템의 시계의 의해서 결정된다.

## 30.1 Date 생성자 함수

1970년도 1월 1일 0시 정각을 기분으로 밀리초 단위의 정수를 Date 생성자 함수로 생성된 객체는 지니고 있다. 다른 날짜와 시간을 다루고 싶은 경우 인수를 지정하여 생성해야 한다.

### 30.1.1 new Date()

생성자 함수를 이용하여 Date 객체를 반환할 수 있고 현재 시스템의 시간을 알려준다. new 를 생략할 경우 객체가 아닌 문자열을 반환한다.

```js
console.log(new Date());
console.log(Date());
```

### 30.1.2 new Date(ms);

인수로 밀리초를 전달하면 기점되는 시간 기준으로 인수에 밀리초만큼 경과한 날짜 및 시간 Date 객체를 반환한다.

```js
new Date(86400000); // Fri Jan 02 1970 09:00:00 GMT+0900 (한국 표준시)
```

### 30.1.3 new Date(DateString)

인수로 날짜와 시간을 지정하는 문자열을 전달하면 지정한 날짜와 시간을 나타내는 Date 개게를 반환한다. 이 때 문자열은 Date.parse 메서드에 의해 해석 가능한 형식이어야 한다.

```js
new Date("May 31, 1994 10:00:00");
new Date("1994/05/31/ 10:00:00");
```

### 30.1.4 new Date(year, month[, day, hour, minute, second, ms])

인수로 연월일시분초밀리초 를 주면 지정한 날짜와 시간을 갖는 Date 객체를 반환한다.  
연, 월을 반드시 지정되어야 하며 그 외 인수는 안 정할 경우 0, 1로 초기화 된다.

> 🚨&nbsp; 월은 0 부터 시작한다. 0 ~ 11

```js
new Date(2022, 3);
```

## 30.2 Date 메서드

### 30.2.1 Date.now

기점으로 현재까지 경과한 밀리초 반환

```js
Date.now(); // 1664064709199
```

### 30.2.2 Date.parse

기점 시간에서 인수로 전달된 지정시간까지 밀리초를 반환

```js
Date.parse("May 31, 1994 10:00:00"); // 770346000000
Date.parse("1994/05/31/ 10:00:00"); // 770346000000
```

### 30.2.3 Date.UTC

기점 시간에서 인수로 전달된 지정시간까지 밀리초를 반환하고 인수의 형식은 연월일시분초 형식의 인수로 전달해야된다.

```js
Date.UTC(2022, 3, 4); // 1649030400000
Date.UTC("1994/05/31/ 10:00:00"); // NaN
```

### 30.2.4 Date.prototype.getFullYear

Date 객체의 연도를 반환한다.

```js
new Date(2022, 3, 4).getFullYear(); // 2022
```

### 30.2.5 Date.prototype.setFullYear

Date 객체에 연도를 나타내는 정수를 설정한다. 옵션으로 월, 일도 가능하다

```js
const date = new Date();

date.setFullYear(2000);
date.getFullYear(); // 2000

date.setFullYear(1994, 4, 31);
date.getFullYear(); // 1994
```

### 30.2.6 Date.prototype.getMonth

Date 객체의 월을 나타내는 0 ~ 11 정수를 반환

```js
new Date("2022/03/24").getMonth(); // 2
```

### 30.2.7 Date.prototype.setMonth

Date 객체의 월을 설정한다. 옵션으로 일도 선택이 가능하다.

```js
const date = new Date();

date.setMonth(3);
date.getMonth(); // 3

date.setMonth(4, 31);
date.getMonth(); // 4
```

### 30.2.8 Date.prototype.getDate

Date 객체의 일을 나타내는 정수를 반환

```js
new Date("2022/03/24").getDate(); // 24
```

### 30.2.9 Date.prototype.setDate

Date 객체의 일을 수정

```js
const date = new Date();

date.setDate(1);
date.getDate(); // 1
```

### 30.2.10 Date.prototype.getDay

Date 객체의 요일을 나타내는 정수를 반환한다.  
0 ~ 6 이며 순서대로 일 ~ 토 순서이다.

```js
new Date("2020/07/24").getDay(); // 5
```

### 30.2.11 Date.prototype.getHours

Date 객체의 시간을 반환한다.

```js
new Date("2020/07/24/ 10:00").getHours(); // 10
```

### 30.2.12 Date.prototype.setHours

Date 객체의 시간을 설정 옵션으로 분, 초, 밀리초도 가능하다.

### 30.2.13 Date.prototype.getMinutes

### 30.2.14 Date.prototype.setMinutes

### 30.2.15 Date.prototype.getSeconds

### 30.2.16 Date.prototype.setSeconds

### 30.2.17 Date.prototype.getMilliseconds

### 30.2.18 Date.prototype.setMilliseconds

### 30.2.19 Date.prototype.getTime

UTC 기점시간으로부터 Date 객체의 시간까지의 경과된 밀리초를 반환

```js
new Date("2022/07/24/ 12:30").getTime(); // 1658633400000
```

### 30.2.20 Date.prototype.setTime

Date 객체에서 UTC 기점시간으로부터 경과된 밀리초를 설정한다.

```js
const date = new Date();
date.setTime(1658633400000);
console.log(date); // Sun Jul 24 2022 12:30:00 GMT+0900 (한국 표준시)
```

### 30.2.21 Date.prototype.getTimezondOffset

UTC와 Date 객체에 저장된 로컬 시간과의 차이를 분 단위로 반환

```js
const date = new Date();
date.getTimezoneOffset(); // -540
```

### 30.2.22 Date.prototype.toDateString

사람이 읽을 수 있는 형식의 문자열로 Date 객체의 날짜를 반환한다.

```js
const date = new Date("2020/07/24/ 10:00");
date.toString(); // 'Fri Jul 24 2020 10:00:00 GMT+0900 (한국 표준시)'
date.toDateString(); // 'Fri Jul 24 2020'
```

### 30.2.23 Date.prototype.toTimeString

사람이 읽을 수 있는 형식의 문자열로 Date 객체의 시간을 반환한다.

```js
const date = new Date("2020/07/24/ 10:00");
date.toString(); // 'Fri Jul 24 2020 10:00:00 GMT+0900 (한국 표준시)'
date.toTimeString(); // '10:00:00 GMT+0900 (한국 표준시)'
```

### 30.2.24 Date.prototype.toISOString

ISO 8601 형식으로 Date 객체의 날짜와 시간을 표현한 문자열을 반환한다.

```js
const date = new Date("2020/07/24/ 10:00");
date.toString(); // 'Fri Jul 24 2020 10:00:00 GMT+0900 (한국 표준시)'
date.toISOString(); // '2020-07-24T01:00:00.000Z'
```

### 30.2.25 Date.prototype.toLocaleString

인수로 전달한 로컬을 기준으로 Date 객체의 날짜와 시간을 표현한 문자열을 반환한다. 인수를 생략한 경우 현재 브라우저가 동작중인 시스템을 로컬을 적용한다.

```js
const date = new Date("2020/07/24/ 10:00");
date.toString(); // 'Fri Jul 24 2020 10:00:00 GMT+0900 (한국 표준시)'
date.toLocaleString(); // '2020. 7. 24. 오전 10:00:00'
date.toLocaleString("ko-KR"); // '2020. 7. 24. 오전 10:00:00'
date.toLocaleString("en-US"); // '7/24/2020, 10:00:00 AM'
date.toLocaleString("ja-JP"); // '2020/7/24 10:00:00'
```

### 30.2.26 Date.prototype.toLocaleTimeString

인수로 전달한 로컬을 기준으로 Date 객체의 시간을 문자열로 반환한다. 인수를 생략한 경우 현재 브라우저가 동작중인 시스템을 로컬을 적용한다.

```js
const date = new Date("2020/07/24/ 10:00");

date.toLocaleTimeString(); // '오전 10:00:00'
date.toLocaleTimeString("ko-KR"); // '오전 10:00:00'
date.toLocaleTimeString("en-US"); // '10:00:00 AM'
date.toLocaleTimeString("ja-JP"); // '10:00:00'
```

## 30.3 Date 를 활용한 시계 예제

setTimeout 을 활용한 1초마다의 시계예제.  
추가 설명이 필요한 부분은 없어보여 예제 생략

# 🔥 추가학습 필요

- push 메서드 성능
- 유니코드 코드 포인트 - 한글 인코딩의 이해 1편
- quicksort, timesort

<hr/>

_개인이 참고하고자 작성한 글이며, 잘못된 정보가 있을 수 있습니다. 잘못된 정보는 메일로 보내주시면 감사하겠습니다._ 🙏
