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

# 37. Set 과 Map

## 37.1 Set

Set 객체는 중복되지 않는 유일한 값들의 집합이다. 배열과 유사하지만 아래와 같이 다른 점이 있다.

- 동일한 값을 중복으로 가질 수 없다.
- 순서를 가지지 않는다.
- 인덱스로 요소에 접근 할 수 없다.

이러한 특성은 수학적 집합의 특성과 일치하며 이를 구현하기 위한 자료구조가 Set 이다. Set 을 통해 교집합, 합집합 등을 구현할 수 있다.

### 37.1.1 Set 객체 생성

생성자 함수로 생성하고 인수를 전달하지 않으면 빈 객체가 생성된다.  
**Set 생성자 함수는 이터러블을 인수로 전달받아 객체를 생성하며 중복된 값은 저장되지 않는다.**

```js
const set = new Set([1, 2, 3, 3]);
console.log(set); // Set(3) {1, 2, 3}
```

### 37.1.2 요소 개수 확인

Set 객체의 요소 개수를 확인할 때는 Set.prototype.size 를 사용하면 되고 이 프로퍼티는 getter 함수만 존재하는 접근자 프로퍼티이므로 임의로 값으로 변경할 수 없다.

```js
const set = new Set([1, 2, 3, 3]);
console.log(set.size); // 3

set.size = 10;
console.log(set.size); // 3
```

### 37.1.3 요소 추가

Set.prototype.add 메서드를 사용한다. 이 메서드는 새로운 요소가 추가된 Set 객체를 반환하기 때문에 메서드 체이닝을 할 수 있다.  
Set 객체는 자바스크립트의 모든 값을 요소로 저장할 수 있으며 중복을 허용하지 않는 특성으로 인해 당연히 중복값의 추가는 무시되며 ~~😳에러가 발생하지는 않는다!~~ 특이점으로 일치 비교 연산자 `===` 에서 다르다고 평가하는 `NaN` 도 Set 에서는 중복으로 취급되어 추가 입력이 되지 않는다.

```js
const set = new Set();

set
  .add(1)
  .add("a")
  .add("1")
  .add(true)
  .add(NaN)
  .add(NaN)
  .add(+0)
  .add(-0)
  .add({});

console.log(NaN === NaN); // false
console.log(set); // Set(7) {1, 'a', '1', true, NaN, 0, {}}
```

### 37.1.4 요소 존재 확인

Set.prototype.has 메서드 사용

```js
const set = new Set([1, 2, 3]);

console.log(set.has(2)); // true
console.log(set.has(89)); // false
```

### 37.1.5 요소 삭제

Set.prototype.delete 메서드를 사용하고 이는 삭제 성공유무를 boolean 값으로 반환한다.  
**🚨 Set 객체는 삭제하려는 요소를 인수로 주어야한다.**

```js
const set = new Set([1, 2, 3]);

console.log(set.delete(2)); // true
console.log(set); //  Set(2) {1, 3}
console.log(set.delete(54)); // false
console.log(set); // Set(2) {1, 3}
```

### 37.1.6 요소 일괄 삭제

Set.prototype.clear 를 사용한다. 해당 메서드는 언제나 undefined 를 반환한다.

```js
const set = new Set([1, 2, 3]);

console.log(set.clear()); // undefined
console.log(set); // Set(0) {size: 0}
```

### 37.1.7 요소 순회

Set.prototype.forEach 메서드를 사용하며 이 메서드는 콜백함수를 인수로 받고 이 콜백함수는 다음과 같은 인수를 같는다.

- 첫 번째 인수: 현재 순회 중인 요소값
- 두 번째 인수: 현재 순회 중인 요소값
- 세 번째 인수: 현재 순회 중인 Set 객체 자체

Array.prototype.forEach 와 유사하다. 애초에 인터페이스를 통일하기 위해서 같은 형식으로 작성되었으며 두 번째 인수만 다름을 알 수 있다.

> 🚧 &nbsp; Set 객체는 이터러블이다 따라서 순회할 수 있으며 스프레드, 디스트럭쳐링이 가능하다. 또한 요소에 순서를 갖지 않지만 순회할 때 요소에 추가된 순서를 따른다.  
> 이는 ECMAScript 사양은 아니지만 이터러블의 순회와 호환성을 유지하기 위함이다.

```js
const set = new Set([1, 2, 3]);

set.forEach((v, _v, set) => {
  console.log(v, _v, set); // 1 1 Set(3) {1, 2, 3}
});
for (const value of set) {
  console.log(value);
}
console.log([...set]); // [1, 2, 3]
```

### 37.1.8 집합 연산

**교집합**

```js
Set.prototype.intersection = function (set) {
  return new Set([...this].filter((v) => set.has(v)));
};

const a = new Set([1, 2, 3]);
const b = new Set([3, 4, 5]);
console.log(a.intersection(b)); // Set(1) {3}
```

**합집합**

```js
Set.prototype.union = function (set) {
  return new Set([...this, ...set]);
};

const a = new Set([1, 2, 3]);
const b = new Set([3, 4, 5]);
console.log(a.union(b)); // Set(5) {1, 2, 3, 4, 5}
```

**차집합**

```js
Set.prototype.difference = function (set) {
  return new Set([...this].filter((v) => !set.has(v)));
};

const a = new Set([1, 2, 3]);
const b = new Set([3, 4, 5]);
console.log(a.difference(b)); // Set(2) {1, 2}
```

**부분 집합과 상위 집합**

```js
Set.prototype.isSuperset = function (set) {
  return [...set].every((v) => [...this].includes(v));
};

const a = new Set([1, 2, 3, 4, 5]);
const b = new Set([3, 4, 5]);
console.log(a.isSuperset(b)); // true
console.log(b.isSuperset(a)); // false
```

## 37.2 Map

Map 객체는 키와 값의 쌍으로 이루어진 컬랙션이지만 미묘한 차이가 있다.

<table>
  <thead>
    <tr>
      <th>구분</th>
      <th>객체</th>
      <th>Map</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>키의 타입</td>
      <td>문자열, 심벌</td>
      <td>모든 값</td>
    </tr>
    <tr>
      <td>이터러블</td>
      <td>X</td>
      <td>O</td>
    </tr>
    <tr>
      <td>요소 개수 확인</td>
      <td>Object.keys(obj).length</td>
      <td>map.size</td>
    </tr>
  </tbody>
</table>

### 37.2.1 Map 객체의 생성

Map 생성자 함수로 생성한다. **Map 생성자 함수는 이터러블을 인수로 전달받아 생성되며, 이 때 이터러블은 키와 값의 쌍으로 이루어진 요소로 구성되어야 한다.**  
중복된 키를 가질 경우 값이 덮여쓰여지며 따라서 중복된 키는 존재할 수 없다.

```js
const map = new Map([
  [1, "a"],
  [1, "d"],
  [2, "b"],
  [3, "c"],
]);
console.log(map); // Map(3) {1 => 'd', 2 => 'b', 3 => 'c'}
```

### 37.2.2 요소 개수 확인

Map.prototype.size 프로퍼티 사용
Set 객체와 동일하다. 접근자 프로퍼티이고 임의로 변경할 수 없다.

```js
const map = new Map([
  [1, "a"],
  [2, "b"],
  [3, "c"],
]);
console.log(map.size); // 3

map.size = 10; // 무시됨
console.log(map.size); // 3
```

### 37.2.3 요소 추가

Map.prototype.set 메서드를 사용한다. 새로운 요소가 추가된 Map 객체를 반환하며 이를 통해 메서드 체이닝이 가능하다. 키에 어떠한 값도 올 수 있으며 키의 중복 확인은 Set 객체의 중복 체크와 동일한 것 같은 느낌이다. (스펙 안 열어봄 그렇게 추측된다. 🙄)

```js
const map = new Map();
const obj = {};

map
  .set(1, "adf")
  .set(NaN, 1)
  .set(NaN, 2)
  .set("key", 3893)
  .set("1", 389328)
  .set(obj, 393);

console.log(map); // Map(5) {1 => 'adf', NaN => 2, 'key' => 3893, '1' => 389328, {…} => 393}
```

### 37.2.4 요소 취득

Map.prototype.get 메서드를 사용한다. get 메서드의 인수로 키를 전달하면 값을 반환하고 없을 경우 undefined 를 반환한다.

```js
const map = new Map([
  [1, "a"],
  [2, "b"],
  [3, "c"],
]);

console.log(map.get(1)); // a
console.log(map.get("af")); // undefined
```

### 37.2.5 요소 존재 여부 확인

Map.prototype.has 메서드를 사용한다. has 메서드는 키를 인수로 받으며 존재 여부에 따라 boolean 값을 반환한다.

```js
const map = new Map([
  [1, "a"],
  [2, "b"],
  [3, "c"],
]);

console.log(map.has(1)); // true
console.log(map.has("af")); // false
```

### 37.2.6 요소 삭제

Map.prototype.delete 메서드를 사용하고 인수로 키를 사용한다. 삭제 성공 여부를 boolean 값으로 반환한다. 없을 경우 무시한다.

```js
const map = new Map([
  [1, "a"],
  [2, "b"],
  [3, "c"],
]);

console.log(map.delete(1)); // true
console.log(map); // Map(2) {2 => 'b', 3 => 'c'}
console.log(map.delete("af")); // false
console.log(map); // Map(2) {2 => 'b', 3 => 'c'}
```

### 37.2.7 요소 일괄 삭제

Map.prototype.clear 메서드를 사용한다. undefined 를 반환한다.

```js
const map = new Map([
  [1, "a"],
  [2, "b"],
  [3, "c"],
]);

console.log(map.clear()); // undefined
console.log(map); // Map(0) {size: 0}
```

### 37.2.8 요소 순회

Map.prototype.forEach 메서드를 사용한다. 콜백함수를 인자로 받으며 콜백함수는 다음과 같은 인수로 받는다.

- 첫 번째 인수: 현재 순회 중인 요소값
- 두 번째 인수: 현재 순회 중인 요소키
- 세 번째 인수: 현재 순회 중인 Map 객체

> 🚧 &nbsp; Map 객체는 이터러블이다 따라서 순회할 수 있으며 스프레드, 디스트럭쳐링이 가능하다. 또한 요소에 순서를 갖지 않지만 순회할 때 요소에 추가된 순서를 따른다.  
> 이는 ECMAScript 사양은 아니지만 이터러블의 순회와 호환성을 유지하기 위함이다.

Map 객체는 이터러블이면서 동시에 이터레이터인 객체를 반환하는 메서드를 제공한다.~~Set 객체도 있기는 한데 왜 언급이 없었는지 모르겠다.~~

- Map.prototype.keys
- Map.prototype.values
- Map.prototype.entries

```js
const map = new Map([
  [1, "a"],
  [2, "b"],
  [3, "c"],
]);

map.forEach((v, k, m) => {
  console.log(v, k, m); // a 1 Map(3) {1 => 'a', 2 => 'b', 3 => 'c'}
});

for (const entry of map) {
  console.log(entry); // [1, 'a']
}
console.log([...map]); // [Array(2), Array(2), Array(2)]

for (const key of map.keys()) {
  console.log(key); // 1
}
```

## 38. 브라우저의 렌더링 과정

~~글도 중요하지만 전반적으로 이미지가 중요한 장이었다고 생각한다. 이미지까지 첨부할 정성은 없어서 이미지는 찾아보시길💩~~

Node.js 등장으로 웹 브라우저에서 벗어나 서버 사이드 개발에서도 사용하는 범용 개발 언어가 된 자바스크립트이지만 아직까지 브라우저 환경에서 동작하는 경우가 많다. 따라서 브라우저의 환경을 고려할 때 더 효율적인 프로그래밍이 가능하다.

따라서 브라우저가 HTML, CSS, 자바스크립트로 작성된 텍스트 문서를 어떻게 파싱하여 브라우저에 렌더링하는지 살펴보자

- 파싱: 프로그래밍 언어의 문법에 맞게 작성된 텍스트 문서를 읽어 들여 실행하기 위해 텍스트 문서의 문자열을 토큰으로 분해하고, 토큰에 문법적인 의미와 구조를 반영하여 트리 구조의 자료구조인 파스 트리를 생성하는 일련의 과정을 말한다. 일반적으로 파싱이 완료된 후 파스 트리를 기반으로 중간 언어인 바이트코드를 생성하고 실행한다.

- 렌더링: HTML, CSS, 자바스크립트로 작성된 문서를 파싱하여 브라우저에서 시각적으로 출력하는 것

**랜더링 과정**

1. 랜더링에 필요한 리소를 요청하고 서버로부터 응답을 받는다.
2. 렌더링 엔진은 받은 응답의 HTML, CSS를 파싱하여 DOM, CSSOM 를 생성하고 이들을 결합하여 랜더링 트리를 생성한다.
3. 자바스크립트 엔진은 응답받은 자바스크립트를 파싱하여 AST 를 생성하고 바이트코드로 변환하여 샐행한다. 이때 자바스크립트는 DOM이나 CSSOM 을 변경할 수 있고 변경된 DOM, CSSOM 은 다시 랜더 트리로 결합된다.
4. 랜더 트리를 기반으로 HTML 요소의 레이아웃을 계산하고 브라우저 화면에 HTML 요소를 페인팅한다.

## 38.1 요청과 응답

필요한 리소를 요청하고 응답받은 리소스를 파싱하여 렌더링하는 것.
브라우저 주소창에 주소(URL)을 입력하면 보편적으로 index.html 을 응답을 하고 연결되어 있는 필요한 리소스들도 같이 호출이 된다. 개발자 도구에서 Network 탭을 확인하면 쉽게 확인 할 수 있다.

- URI(Uniform Resource Idntifier): 특정 리소스를 식별하는 통합 자원 식별자
- URL(Uniform Resource Locator): 리소스의 어디에 위치하는지 표기
- URN(Uniform Resource Name): 리소스를 특정하는 이름

## 38.2 HTTP 1.1, HTTP 2.0

HTTP 는 웹에서 브라우저와 서버가 통신하기 위한 프로토콜

- HTTP/1.0: 1996 발표
- HTTP/1.1: 1999 발표, 기본적으로 커넥션당 하나의 요청과 응답만 처리
- HTTP/2.0: 2015 발표, 커넥션당 동시 전송 가능 1.1 대비 50% 정도 향상 추정

~~2022년 6월에 HTTP/3 가 표준화되었다.~~

## 38.3 HTML 파싱과 DOM 생성

HTML 문서는 문자열로 이루어진 순수한 텍스트이고 이 문서를 브라우저에 시각적인 픽셀로 렌더링 하려면 브라우저가 이해할 수 있는 자료구조로 변환하여 메모리에 저장해야 한다. 브라우저 랜더링 엔진은 이러한 자료구조 DOM 을 생성한다.

1. 서버가 요청받은 HTML 문서를 바이트로 인터넷을 경유하여 응답
2. 브라우저가 바이트로 형태로 응답을 받고 meta 태그 charset 속성에 지정된 방식에 따라 문자열로 변환한다.(이는 응답 헤더에 있다.)
3. 문자열로 변환된 문서를 읽어 문법적 의미를 지니는 최소 단위 토큰으로 분해한다.
4. 각 토큰을 객체로 변환하여 노드로 생성한다. 토큰의 내용에 따라 문서 노드, 텍스트 노드 등이 생성되고 DOM의 기본 구성 요소가 된다.
5. HTML 문서는 HTML 요소들의 집합으로 이루어지며 이 요소는 중첩 관계를 갖는다. 이러한 요소 간의 관계를 반영하여 트리 자료구조를 구성하고 이를 `DOM`이라 한다.

**DOM은 HTML 문서를 파싱한 결과물이다.**

## 38.4 CSS 파싱과 CSSOM 생성

DOM 생성 중 CSS 를 로드하는 link, style 태그를 만나면 생성을 중지하고 CSS 를 HTML 과 동일한 파싱과정(바이트 - 문자 - 토큰 - 노드 - CSSOM)을 통해 해석하여 CSSOM 을 생성한다.  
파싱이 완료되면 중지된 DOM 생성을 재개한다.

## 38.5 랜더 트리 생성

렌더링 엔진은 파싱되어 생성된 DOM, CSSOM 을 결합하여 렌더 트리를 만든다. 이 렌더 트리는 랜더링을 위한 트리 구조의 자료구조이다. ~~(화면에 표기되지 않는 노드들은 포함되지 않는다.)~~

이 렌더 트리는 각 요소의 레이아웃(위치와 크기)를 계산하는데 사용되며 브라우저 화면에 픽셀을 렌더링하는 페인팅 처리에 입력된다.  
이러한 렌더링 과정은 반복되서 실행될 수 있는데 이는 비용이 많이 드는 작업으로 성능에 악영향을 주므로 주의가 필요하다.

- 자바스크립트에 의한 노드 추가 또는 삭제
- 브라우저 창의 리사이징에 의한 뷰포트 크기 변경
- HTML 요소의 레이아웃을 변경을 발생시키는 스타일 변경

## 38.6 자바스크립트 파싱과 실행

CSS 파싱과정과 마찬가지로 일반적으로 DOM 생성 중 자바스크립트를 로드하거나 콘텐츠로 담은 script 태그를 만나면 DOM 생성이 일시 중단된다. 이후 자바스크립트 파싱이 완료되면 재개된다.

자바스크립트 파싱과 실행은 브라우저의 렌더링 엔진이 아닌 자바스크립트 엔진이 처리하는데 엔진은 자바스크립트 코드를 파싱하여 CPU가 이해할 수 있는 저수준 언어로 변환하고 실행하는 역할을 한다. 대표적으로 구글 크롬의 V8 엔진이 있다. 브라우저에 따라 다른 자바스크립엔진을 사용할 수 있으나 ECMAScript 사양을 준수하고 있다.~~아마도...?~~

렌더링 엔진으로부터 제어권을 넘겨받은 자바스크립트 엔진은 자바스크립트 코드를 파싱하기 시작하고 해석하여 `AST`를 생성하고 이를 기반으로 인터프리터가 실행 할 수 있는 중간 코드인 바이트코드를 만든다.

**토큰나이징**

단순한 문자열인 자바스크립트 소스코드를 어휘 분석하여 문법적 의미를 갖는 코드의 최소 단위인 토큰들로 분해한다. 이 과정을 렉싱이라고 부르기도 하지만 미묘한 차이가 있다.

**파싱**

토큰들의 집합을 구문 분석하여 AST 을 생성한다. AST는 토큰에 문법적인 의믜와 구조를 반영한 트리 구조의 자료구조이다. AST는 인터프리터나 컴파일러만 사용하는 것이 아니라 트랜스파일러들 구현할 때도 사용할 수 있다.

**바이트코드 생성과 실행**

파싱의 결과물인 AST는 인터프리터가 실행할 수 있는 중간 코드인 바이트코드로 변환되고 인터프리터에 의해 실행된다.

## 38.7 리플로우와 리페인트

자바스크립트 코드에 DOM이나 CSSOM 을 변경하는 경우 렌더트리가 다시 결합되고 리렌더링이 된다. 이를 리플로우 또는 리페인트라고 한다.

- 리플로우: 레이아웃 계산을 다시 해야 할 때
- 리페인트: 페인트만 다시 할 경우

## 38.8 자바스크립트 파싱에 의한 HTML 파싱 중단

일반적으로 렌더링 엔진과 자바스크립트 엔진은 병렬로 처리되지 않고 직렬적으로 파싱을 수행한다. 이는 script 태그의 위치가 중요하다는 것을 의미한다.

보통 다음과 같은 이유로 body 요소 아래에 많이 작성한다.

- DOM이 완성되지 않은 상태에서 자바스크립트가 DOM 을 조작하면 에러가 발생할 수 있다.
- 자바스크립트 로딩/파싱/실행으로 인해 HTML 요소드르이 렌더링에 지장받는 일이 발생하지 않아 페이지 로딩 시간이 단축된다.

## 38.9 script 태그의 async/defer 속성

앞서 말한 자바스크립트 파싱에 의한 DOM 생성이 중단되는 문제를 근본적으로 해결하기 위한 HTML5 부터 async, defer 속성이 추가되었다.  
src 속성을 통해 외부 자바스크립트를 파일을 로드하는 경우 사용할 수 있고 **🚨 없는 경우에는 사용할 수 없다**

```HTML
<script async src="extern.js"></script>
<script defer src="extern.js"></script>
```

두 속성은 비동기적으로 로드가 진행되는 점은 같지만 실행 시점이 좀 다르다.

- async: 자바스크립트가 로드가 완료된 시점에 파싱/실행이 되며 이때 HTML 파싱 중일 경우 중단된다.
- defer: DOM 생성이 완료된 직후 파싱/실행이 된다.

# 39. DOM

DOM은 HTML 문서의 계층적 구조와 정보를 표현하며 이를 제어할 수 있는 API, 즉 프로퍼티와 메서드를 제공하는 트리 자료구조이다.

~~DOM 에 관한 장이다보니 예제가 HTML 및 이미지이다. 대부분 누락되었다.💦~~

## 39.1 노드

### 39.1.1 HTML 요소와 노드 객체

HTML 요소는 HTML 문서를 구성하는 개별적인 요소를 의미한다. 이 요소는 렌더링 엔진에 의해 파싱되어 DOM 을 구성하는 요소 노드 객체로 변환된다.

```HTML
<!-- HTML 요소 -->
<div class="greeting">Hello world!</div>
```

HTML 요소는 시작태그, 종료태그, 속성이름, 속성값, 콘텐츠 로 이루어져 있으며, 속성은 속성(어트리뷰트) 노드, 텍스트 콘텐츠는 텍스트 노드로 변환된다.  
또한 HTML 요소는 중첩 관계를 갖고 이로 인해 계층적인 부자 관계가 생성된다 이러한 관계를 반영하여 노드 객체들을 트리 자료 구조로 구성한다.

**노드 객체들로 구성된 트리 자료구조를 DOM(트리) 이라 한다**

> 트리 구조는 검색을 해보면 양질의 다양한 자료를 만날 수 있다.

### 39.1.2 노드 객체의 타입

~~예제 생략~~

DOM은 노드 객체의 계층적인 구조로 구성된다. 노드 객체는 종류가 있고 상속 구조를 갖는다. 노드 객체는 총 12개 종류가 있고 책에서는 중요한 4가지를 설명하고 있다.

**문서 노드**

DOM 트리 최상위에 존재하는 루트 노드, document 객체를 지칭. document 객체는 브라우저가 렌더링한 HTML 문서 전체를 가리키는 객체로 전역 객체에 바인딩 되어 있다.  
브라우저 환경의 모든 자바스크립트 코드는 script 태그에 의해 분리되어 있어도 하나의 전역을 공유하므로 하나의 document 객체를 바라보고 이는 유일하다.  
DOM 트리의 노드들에 접근하기 위한 진입점 역할을 담당한다.

**요소 노드**

HTML 요소를 가리키는 객체이다. 문서의 구조를 표현한다.

**어트리뷰트 노드**

HTML 요소의 어트리뷰트를 가리키는 객체이다. 어트리뷰트 노드는 본래 지정되어 있는 HTML 요소의 요소 노드와 연결되어 있다. 따로 부모 노드가 있지 않으므로 어트리뷰트 노드에 접근하기 위해서는 먼저 요소 노드에 접근해야 한다.

**텍스트 노드**

HTML 요소의 텍스트를 가리키는 객체이며 문서의 정보를 표현한다. 자식 노드를 가질 수 없는 리프 노드이며 DOM 트리의 최종단이다.

### 39.1.3 노드 객체의 상속 구조

DOM 을 구성하는 노드 객체는 자신의 구조와 정보를 제어할 수 있는 DOM API 를 사용하여 자신의 부모, 형제, 자식을 탐색할 수 있으며, 자신의 어트리뷰트와 텍스트를 조작할 수 있다.

노드 객체는 ECMAScript 사양에 정의된 표준 빌트인 객체가 아니라 브라우저에서 제공하는 호스트 객체이지만 노드 객체도 자바스크립트 객체이므로 프로토타입에 의한 상속 구조를 갖는다.

모든 노든 객체는 Object, EventTarget, Node 인터페이스를 상속받는다. 또한 각 노드는 여기서 세분화 되어 각 노드에 맞는 인터페이스를 추가적으로 상속 받는다 **따라서 프로토타입 체인에 있는 모든 프로토타입의 프로퍼티나 메소드를 사용할 수 있고 배열이 배열이면서 객체인 것 처럼 다양한 특성을 지닐 수 있게 된다.**

> 노드 객체의 상속 구조는 개발자 도구 Elements 패널 우측의 Properties 에서 확인가능

## 39.2 요소 노드 취득

요소 노드의 취득은 HTML 요소를 조작하는 시작점이고, DOM은 요소 노드를 취득할 수 있는 다양한 메서드를 제공한다.

### 39.2.1 id를 이용한 노드 취득

`Document.prototype.getElementById` 메서드를 사용할 수 있다. id 값은 문서 내에서 유일한 값이어야 하며 여러개의 값을 가질 수도 없다. 해당 메서드와 관련하여 아래와 같은 사항이 있다.

- 중복되어 입력하여도 오류는 발생하지는 않지만 첫 번째 요소 노드만 찾을 수 있다.
- id 가 없을 경우는 null을 반환한다.
- HTML 요소에 id 어트리뷰트를 부여하면 id 값과 동일한 이름의 전역 변수가 암묵적으로 선언되고 해당 노드 객체가 할당되는 부수 효과가 있다.
- id 값과 동일한 전역 변수가 이미 선언되었다면 이 전역 변수에 노드 객체가 재할당되지 않는다.

### 39.2.2 태그 이름을 이용한 요소 노드 취득

`Document.prototype/Element.prototype.getElementsByTagName` 메서드를 사용하여 태그를 사용하여 요소 노드들을 취득할 수 있다. 이 메서드는 여러 개의 요소 노드 객체를 갖는 DOM 컬랙션 객체인 HTMLCollection 객체를 반환한다.

- Document.prototype.getElementsByTagName : 문서 노드부터 탐색한다.
- Element.prototype.getElementsByTagName : 특정 요소 노드부터 탐색한다.

> HTMLCollection 객체는 유사 배열 객체이면서 이터러블이다.

HTML 문서의 모든 요소 노드를 취득하고 싶다면 인수로 `*` 를 전달하면 된다. 일치하는 요소노드가 없을 경우 빈 HTMLCollection 객체를 반환한다.

### 39.2.3 class 를 이용한 요소 노드 취득

`Document.prototype/Element.prototype.getElemnetsByClassName` 메서드를 사용하여 인수로 class 어트리뷰트의 값을 전달하면 해당하는 요소들을 HTMLCollection 객체에 담아 반환한다. getElementsByTagName 와 동일한 특성들을 지니고 있다.

### 39.2.4 CSS 선택자를 이용한 요소 노드 취득

`Document.prototype/Element.prototype.querySelector` 메서드에 인수로 CSS 선택자를 전달하여 일치하는 하나의 요소 노드를 탐색하여 반환한다.

- 인수로 전달한 CSS 선택자를 만족시키는 요소 노드가 여러 개인 경우 첫 번째 요소 노드만 반환
- 인수로 전달한 CSS 선택자를 만족시키는 요소 노드가 없을 경우 null 반환
- 인수로 전달한 CSS 선택자가 문법에 맞지 않는 경우 DOMException 발생

```css
/* 전체 */
* {
}
/* 태그 */
div {
}
/* id */
#id {
}
/* class */
.class {
}
/* 어트리뷰트 */
input[type="text"] {
}
/* 인접 형제 */
p + ul {
}
/* 일반 형제 */
p ~ ul {
}
```

여러개를 선택하고자 하는 경우는 `Document.prototype/Element.prototype.querySelectorAll` 메서드를 사용하면 된다. 일치하는 모든 요소 노드를 NodeList 객체에 담아서 반환하고 이 객체는 유사 배열 객체이면서 이터러블이다.

- 인수로 전달된 CSS 선택자를 만족시키는 요소가 존재하지 않는 경우 빈 NodeList 객체 반환
- 인수로 전달된 CSS 선택자가 문법에 맞지 않는 경우 DOMException 발생

> 🚧&nbsp; CSS 선택자를 사용하는 querySelector[All] 메서드는 getElementById, getElementsBy\*\*\* 메서드들 보다 느린 것으로 알려져 있는데 좀더 구체적인 요소 노드를 취득할 수 있는 장점이 있다.  
> id 가 있을 경우 getElementById 를 그 외 경우에는 querySelector 종류를 책에서는 권장하고 있다.

### 39.2.5 특정 요소 노드를 취득할 수 있는 확인

`Element.prototype.matches` 메서드는 인수로 전달한 CSS 선택자를 통해 특정 요소 노드를 취득할 수 있는지 확인 가능하다.

> 🚧&nbsp; 이 메서드는 이벤트 위임에 유용하다.

### 39.2.6 HTMLCollection 과 NodeList

`HTMLCollection` 과 `NodeList` 은 DOM 컬랙션 객체로 중요한 특징은 노드 객체의 상태 변화를 실시간으로 반영하는 `살아있는 객체`라는 것이다. HTMLCollection 는 언제나 live 객체로 동작하고 NodeList 는 대부분 non-live 객체로 동작하지만 경우에 따라 live 객체로 동작 할 때가 있다.

**HTMLCollection**

라이브 객체이므로 순회하면서 노드 상태를 변경할 경우 의도하지 않게끔 동작할 확률이 높다. 역방향 순회와 while 을 사용한 회피 방식이 있지만 객체를 배열로 변환하여 사용하는 것을 권장한다.

**NodeList**

일반적으로 non-live 객체이고 forEach, item, entries 등 메서드를 제공하여 HTMLCollection 보다 안전해 보이지만 childNodes 프로퍼티가 반환하는 NodeList 객체는 live 객체로 동작하므로 이또한 객체를 배열로 변환하여 사용하는 것을 권장한다.

## 39.3 노드 탐색

Node, Element 인터페이스는 DOM 트리 상의 노드를 탐색할 수 있도록 트리 탐색 프로퍼티를 제공한다.  
이러한 프로퍼티는 모두 읽기 전용 접근자 프로퍼티이다. 값을 할당할 경우 에러 없이 무시한다.

### 39.3.1 공백 텍스트 노드

HTML 요소 사이의 스페이스, 탭 등 공백 문자는 텍스트 노드를 생성한다. 이를 공백 텍스트 노드라고 한다. 따라서 노드를 탐색할 때 공백 문자가 생성한 공백 텍스트 노드에 주의해야 한다.

### 39.3.2 자식 노드 탐색

- Node.prototype.childNodes : 자식 노드 모두를 탐색하여 반환하며 요소노드뿐만 아니라 텍스트노드도 포함될 수 있다.
- Element.prototype.children : 자식 노드 중에서 요소 노드만 탐색하여 반환한다.
- Node.prototype.firstChild : 첫 번째 자식 노드 반환
- Node.prototype.lastChild : 미지막 자식 노드 반환
- Element.prototype.firstElementChild: 첫 번째 자식 요소 노드를 반환한다.
- Element.prototype.lastElementChild: 마지막 자식 요소 노드를 반환한다.

### 39.3.3 자식 노드 확인

`Node.prototype.hasChildNodes` 메서드를 사용한다. 이는 텍스트 노드를 포함한다는 것을 주의하며 요소 노드가 존재하는지 확인하고 싶다면 children.length 나 Element 인터페이스의 childElementCount 프로퍼티를 사용한다.

### 39.3.4 요소 노드의 텍스트 노드 탐색

요소노드의 텍스트 노드는 자식 노드임으로 firstChild 프로퍼티로 접근할 수 있다. 이 프로퍼티가 반환한 노드는 텍스트 노드이거나 요소 노드다.~~??~~

### 39.3.5 부모 노드 탐색

`Node.prototype.parentNode` 프로퍼티를 사용한다. 다만 부모노드가 텍스트 노드인 경우는 없다.

### 39.3.6 형제 노드 탐색

부모 노드가 같은 형제 노드를 탐색하려면 다음과 같은 노드 탐색 프로퍼티를 사용한다. 단 어트리뷰트 노드는 요소 노드에 연결되어 있지만 부모노드가 같은 형제 노드가 아니기 때문에 반환되지 않는다. ~~??~~

- Node.prototype.previousSibling : 부모 노드가 같은 형제 노드 중 자신의 이전 형제노드
- Node.prototype.nextSibling : 부모 노드가 같은 형제 노드 중 자신의 다음 형제노드
- Elment.prototype.previousElementSibling : 부모 노드가 같은 형제 요소 노드 중 자신의 이전 형제 요소 노드를 반환
- Elment.prototype.nextElementSibling : 부모 노드가 같은 형제 요소 노드 중 자신의 다음 형제 요소 노드를 반환

## 39.4 노드 정보 취득

**Node.prototype.nodType**

노드의 객체의 종류를 나타내는 상수를 반환한다. 이는 Node 에 정의되어 있다.

- Node.ELEMENT_NODE: 요소 노드 타입을 나타내는 상수 1을 반환
- Node.TEXT_NODE: 텍스트 노드 타입을 나타내는 상수 3을 반환
- Node.DOCUMENT_NODE: 문서 노드 타입을 나타내는 상수 9을 반환

**Node.prototype.nodeName**

노드의 이름을 문자열로 반환한다.

- 요소 노드: 대문자 문자열로 태그 이름
- 텍스트 노드: "#text"
- 문서 노드: "#document"

## 39.5 요소 노드의 텍스트 조작

### 39.5.1 nodeValue

`Node.prototype.nodeValue` 프로퍼티를 활용하여 요소 노드의 텍스트를 변경할 수 있다. 문서 노드나 요소 노드에서의 값은 null 이므로 의미가 없다.

1. 텍스트를 변경할 요소 노드를 취득한 다음 텍스트 노드를 탐색
2. 탐색한 텍스트 노드의 nodeValue 프로퍼티에 값 변경

### 39.5.2 textContent

`Node.prototype.textContent` 프로퍼티를 활용하여 요소 노드의 텍스트를 변경할 수 있다.
이 프로퍼티는 요소 노드의 텍스트와 모든 자손 노드의 텍스트르 모두 취득하거나 변경한다.  
중간에 콘텐츠 영역 내에 요소 노드가 있어도 텍스트 노드의 값들만을 반환한다. 값을 가져오는데 있어서는 nodeValue 프로퍼티보다 코드가 간결하다. 새로운 값을 넣을 경우 HTML 마크업이 포함되어 있더라도 기존의 자식노드들은 제거되면서 단순 텍스트로 인지된다.

비슷한 프로퍼티로 innerText 프로퍼티가 있는데 아래와 같은 이유로 사용을 권장하지 않는다.

- innerText 는 CSS에 순종적이다. innerText 프로퍼티는 CSS에 비표시로 지정된 요소 노드의 텍스트를 반환하지 않는다.
- innerText 는 CSS를 고려해야 하므로 textContent 프로퍼티보다 느리다.

# 🔥 추가학습 필요

- Set 기본 메서드 왜 없을까?
- URN
- 렉싱
- 어트리뷰트 노드는 요소 노드의 자식인가?

<hr/>

_개인이 참고하고자 작성한 글이며, 잘못된 정보가 있을 수 있습니다. 잘못된 정보는 메일로 보내주시면 감사하겠습니다._ 🙏
