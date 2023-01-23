# 23. 실행 컨텍스트

자바스크립트의 동작 원리를 담고 있는 핵심 개념이며, 이를 통해 자바스크립트가 스코프를 기반으로 식별자와 식별자에 바인딩된 값을 관리하는 방식,  
호이스팅이 발생하는 이유, 클로저의 동작 방식, 태스크 큐와 함께 동작하는 이벤트 핸들러와 비동기 처리의 동작 방식을 알 수 있다.

## 23.1 소스코드의 타입

ECMAScript 사양은 소스코드(ECMAScript code)를 4가지 타입으로 구분한다. 각 소스코드는 실행 컨텍스트를 생성한다.  
소스코드가 4가지 타입으로 구분하는 이유는 소스코드의 타입에 따라 실행 컨텍스트를 생성하는 과정과 관리 내용이 다르기 때문이다.

<table>
  <thead>
    <tr>
      <th>소스코드의 타입</th>
      <th>설명</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>전역코드(global code)</td>
      <td>전역에 존재하는 소스코드를 지칭. 전역에 정의된 함수, 클래스 등 내부 코드는 미포함</td>
    </tr>
    <tr>
      <td>함수 코드(function code)</td>
      <td>함수 내부의 코드를 지칭. 함수 내부에 중첩된 함수, 클래스 등 내보 코드는 미포함</td>
    </tr>
    <tr>
      <td>eval 코드(eval code)</td>
      <td>빌트인 전역 함수인 eval 함수에 인수로 전달되어 실행되는 소스코드를 지칭</td>
    </tr>
    <tr>
      <td>모듈 코드(module code)</td>
      <td>모듈 내부에 존재하는 소스코드를 지칭. 모듈 내부의 함수, 클래스 등 내부 코드는 미포함</td>
    </tr>
  </tbody>
</table>

각 소스코드는 각자의 스코프와 식별자등의 관리 등을 위해 해당하는 코드들이 평가되면 각자에 맞는 실행 컨텍스트가 된다.

- 전역코드: 전역 실행 컨텍스트
- 함수 코드: 함수 실행 컨텍스트
- eval 코드: eval 실행 컨텍스트
- 모듈 코드: 모듈 실행 컨텍스트

## 23.2 소스코드의 평가와 실행

자바스크립트 엔진은 소스코드를 2개의 과정, `소스코드의 평가` 와 `소스코드의 실행` 과정으로 나누어 처리한다.

- 소스코드의 평가 과정

  1. 실행 컨텍스트 생성
  2. 변수, 함수 등의 선언문 실행
  3. 생성된 변수나 함수 식별자를 키로 실행 컨텍스트가 관리하는 스코프(렉시컬 환경의 환경 레코드)에 등록

<br/>

- 소스코드의 실행 과정(런타임)
  1. 선언문을 제외한 소스코드 순차적 실행
  2. 소스코드 실행에 필요한 정보를 실행 컨텍스트가 관리하는 스코프에서 검색하여 취득
  3. 변수 값 변경 등 소스코드의 실행 결과는 다시 실행 컨텍스트가 관리하는 스코프에 등록

```js
var x;
x = 1;
```

위 예제가 실행되는 순서는 다음과 같다.

1. 선언문 var x; 실행
2. 생성된 변수 식별자 x, 실행 컨텍스트가 관리하는 스코프 등록 및 undefined 로 초기화
3. x = 1; 실행을 위해 실행 컨텍스트가 관리하는 스코프 검색
4. 등록된 변수임을 확인하고 1을 할당
5. 해당 결과를 실행 컨텍스트에 등록하여 관리 ~~??~~

## 23.3 실행 컨텍스트의 역할

다음 예제가 어떻게 평가되고 실행되는지 보자.

```js
// 전역 변수 선언
const x = 1;
const y = 2;

// 함수 정의
function foo(a) {
  // 지역변수 선언
  const x = 10;
  const y = 20;

  //메서드 호출
  console.log(a + x + y);
}

// 함수호출
foo(100); // 130;

// 메서드 호출
console.log(x + y); // 3
```

1. 전역코드 평가
2. 전역코드 실행
3. 함수코드 평가
4. 함수코드 실행

위 예제에서는 크게 위에 같은 순서로 진행되고 이러한 코드가 실행되려면 다음과 같이 스코프, 식별자, 코드 실행 순서 등의 관리가 필요하다.

- 선언에 의해 생성된 식별자를 스코프에 구분하여 등록하고 상태변화를 지속적으로 관리할 수 있어야 함.
- 스코프는 중첩 관계에 의해 스코프 체인을 형성해야 한다.
- 현재 실행 중인 코드의 실행 순서를 변경할 수 있어야 하며 되돌아갈 수 있어야 한다.

이러한 관리를 해주는 것을 실행 컨텍스트라고 하며 **소스코드를 실행하는데 필요한 환경을 제공하고 코드의 실행 결과를 실제로 관리하는 영역**이다.  
식별자와 스코프는 실행 컨텍스트의 `렉시컬 환경`으로 관리하고 코드 실행 순서는 `실행 컨텍스트 스택`으로 관리한다.

## 23.4 실행 컨텍스트 스택

```js
const x = 1;

function foo() {
  const y = 2;

  function bar() {
    const z = 3;
    console.log(x + y + z);
  }
  bar();
}

foo(); // 6
```

자바스크립트 엔진은 코드를 평가 할 때 실행 컨텍스트를 생성하게 되고 이렇게 생성되는 실행 컨텍스트는 스택 자료구조로 관리가 되고 이걸 `실행 컨텍스트 스택`이라고 한다.

위 코드는 아래와 같이 진행된다.

1. 전역 코드의 평가와 실행
2. foo 함수 코드의 평가와 실행
3. bar 함수 코드의 평가와 실행
4. foo 함수 코드로 복귀
5. 전역 코드로 복귀

| 1    |  2   |  3   |  4   |  5   |
| :--- | :--: | :--: | :--: | :--: |
|      |      | bar  |      |      |
|      | foo  | foo  | foo  |      |
| 전역 | 전역 | 전역 | 전역 | 전역 |

실행 컨텍스트 스택은 코드의 실행 순서를 관리하고 최상위에 존재하는 실행 컨텍스트는 언제나 현재 실행 중인 코드의 실행 컨텍스트이다.

## 23.5 렉시컬 환경

렉시컬 환경은 식별자와 식별자에 바인딩된 값, 상위 스코프에 대한 참조를 기록하는 자료구조로 실행 컨텍스트를 구성하는 컴포넌트이다.  
실행 컨텍스트는 `LexicalEnvironment` 컴포넌트와 `VariableEnvironment` 컴포넌트로 구성되는데 생성 초기에는 같은 렉시컬 환경을 참조하지만 이후 몇가지 상황에 의해 `VariableEnvironment` 컴포넌트에 새로운 환경이 생성되고 달라지는 경우가 있다.

렉시컬 환경은 다음과 같은 두 개의 컴포넌트로 구성된다.

1. 환경 레코드: 스코프에 포함된 식별자를 등록하고 바인딩된 값을 관리하는 저장소
2. 외부 렉시컬 환경에 대한 참조: 상위 스코프, 이를 통해 스코프 체인을 구현한다.

## 23.6 실행 컨텍스트의 생성과 식별자 검색 과정

아래 예제의 실행 과정은 책의 그림으로 이해하는게 좋을 듯 싶다.
`P.368`~`P.386` 참조

```js
var x = 1;
const y = 2;

function foo(a) {
  var x = 3;
  const y = 4;

  function bar(b) {
    console.log(a + b + x + y + z);
  }
  bar(10);
}

foo(20);
```

### 23.6.1 전역 객체 생성

전역 객체는 전역 코드가 평가되기 이전에 생성된다.

### 23.6.2 전역 코드 평가

소스코드가 로드되면 자바스크립트 엔진이 전역 코드를 아래와 같은 순서로 평가한다.

1. 전역 실행 컨텍스트 생성
2. 전역 렉시컬 환경 생성  
   2.1 전역 환경 레코드 생성  
    객체 환경 레코드 생성  
    선언적 환경 레코드 생성  
   2.2 this 바인딩  
   2.3 외부 렉시컬 환경 대한 참조 결정

### 23.6.3 전역 코드 실행

### 23.6.4 foo 함수 코드 평가

1. 함수 실행 컨텍스트 생성
2. 함수 렉시컬 환경 생성  
   2.1 함수 환경 레코드 생성  
   2.2 this 바인딩  
   2.3 외부 렉시컬 환경 대한 참조 결정

### 23.6.5 foo 함수 코드 실행

### 23.6.6 bar 함수 코드 평가

### 23.6.7 bar 함수 코드 실행

### 23.6.8 bar 함수 코드 실행 종료

### 23.6.9 foo 함수 코드 실행 종료

### 23.6.10 전역 코드 실행 종료

## 23.7 실행 컨텍스트와 블록 레벨 스코프

블록 레벨 스코프는 선언적 환경 레코드를 갖는 렉시컬 환경을 새롭게 생성하여 기존의 렉시컬 환경과 교체를 하며 새롭게 생성된 렉시컬 환경의 외부 렉시컬 환경 참조는 교체하기 이전의 렉시컬 환경을 가르킨다.

```js
let x = 1;

if (true) {
  let x = 10;
  console.log(x);
}

console.log(x);
```

# 24. 클로저

클로저는 자바스크립트 고유의 개념이 아니며, 함수형 프로그래밍 언어에서 사용되는 중요한 특성이다.  
자바스크립트 고유의 개념이 아니므로 클로저의 정의는 ECMAScript 사양에 등장하지 않지만 MDN 에서는 아래와 같이 정의하고 있다.

> 🚨 &nbsp; 클로저는 함수와 그 함수가 선언된 렉시컬 환경과의 조합이다.

## 24.1 렉시컬 스코프

렉시컬 스코프는 정적 스코프이고 함수를 어디서 호출했는지가 아니라 함수를 어디에 정의했는지에 따라 상위 스코프를 정하므로 아래와 같은 차이가 발생한다.

```js
const x = 1;

function outerFunc() {
  const x = 10;

  function innerFunc() {
    console.log(x); // 10
  }
  innerFunc();
}

outerFunc();
```

```js
const x = 1;

function outerFunc() {
  const x = 10;
  innerFunc();
}

function innerFunc() {
  console.log(x); // 1
}

outerFunc();
```

렉시컬 환경의 외부 렉시컬 환경에 대한 참조에 저장되는 참조값은 함수 정의가 평가되는 시점에 함수가 정의된 환경에 의해 결정 된다.

## 24.2 함수 객체의 내부 슬롯 [[Environment]]

함수는 자신의 내부 슬롯 [[Environment]]에 자신이 정의된 환경, 즉 상위 스코프의 참조를 저장한다.  
이는 자신이 호출되었을 때 생성될 함수 렉시컬 환경의 '외부 렉시컬 환경에 대한 참조'에 저장될 값이고, 함수 객체는 내부 슬롯 [[Environment]]에 저장한 렉시컬 환경의 참조, 즉 상위 스코프를 자신이 존재하는 한 기억한다.

```js
const x = 1;

function foo() {
  const x = 10;

  // 상위 스코프는 함수 정의 환경에 따라 결정
  // 함수 호출 위치와 상위 스코프는 무관
  bar();
}

// bar - 전역 렉시컬 환경을 [[Environment]] 에 저장
function bar() {
  console.log(x);
}

foo();
bar();
```

## 24.3 클로저와 렉시컬 환경

아래 예제처럼 외부 함수보다 중첩 함수가 더 오래 유지되는 경우 중첩 함수는 이미 생명 주기가 종료된 위부 함수의 변수를 참조할 수 있고 이러한 중첩 함수를 클로저라고 부른다.  
여기서 `outer` 함수의 실행 컨텍스트가 먼저 스택에서 제거되지만 `outer` 함수의 렉시컬 환경을 반환된 `inner` 함수 객체의 `[[Environment]]` 슬롯이 참조하고 있으므로 `outer` 함수의 렉시컬 환경까지 소멸하는 것은 아니다.

```js
const x = 1;

function outer() {
  const x = 10;
  const inner = function () {
    console.log(x);
  };
  return inner;
}

const innerFunc = outer();
innerFunc(); // 10
```

자바스크립트의 모든 함수는 상위 스코프를 기억하므로 이론적으로 모든 함수는 클로저이나 일반적으로 모든 함수를 클로저라고 하지 않고 **중첩 함수가 상위 스코프의 식별자를 참조하고 있고 중첩 함수가 외부 함수보다 더 오래 유지되는 경우에 한정하는 것이 일반적이다.**  
~~클로저가 불필요한 메모리의 점유함을 우려할 수 있으나 자바스크립트 엔진은 최적화가 잘 되어 있어 참조하지 않는 식별자는 기억하지 않으므로 크게 걱정할 필요 없다.~~

## 24.4 클로저의 활용

클로저는 상태를 안전하게 변경하고 유지하기 위해 사용된다.  
아래의 예제를 보면 전역에 num 은 접근이 자유롭고 함수 내의 선언한 num 의 경우는 값을 유지할 수 없다. 이러한 상황을 해결하기 위해 클로저를 많이 사용한다.

```js
let num = 0;

const increase = function () {
  // let num = 0;
  return ++num;
};

console.log(increase()); // 1
console.log(increase()); // 2
console.log(increase()); // 3
```

아래에서는 클로저를 사용하여 num 을 은닉하고 상태를 유지할 수 있게끔 작성한 코드이다.

```js
const count = (function () {
  let num = 0;

  return {
    increase() {
      return ++num;
    },
  };
})();

console.log(count.increase()); // 1
console.log(count.increase()); // 2
console.log(count.increase()); // 3
```

외부 상태 변경이나 가변 데이터를 피하고 불변성을 지향하는 함수형 프로그래밍에서 부수 효과를 최대한 억제하여 오류를 피하고 프로그램의 안정성을 높이기 위해서 클로저를 많이 사용한다.

```js
function makeCounter(predicate) {
  let counter = 0;

  return function () {
    counter = predicate(counter);
    return counter;
  };
}

function increase(n) {
  return ++n;
}
function decrease(n) {
  return --n;
}

const increaser = makeCounter(increase);
const decreaser = makeCounter(decrease);

// 값이 공유되지는 않는다.
console.log(increaser()); // 1
console.log(decreaser()); // -1
```

makeCounter 는 보조 함수를 인자로 받아 함수를 반환하는 고차 함수이며 반환되는 함수는 독립된 렉시컬 함수를 가지고 있다.  
값을 공유하고 싶다면 하나의 객체만 만들면된다.

```js
function makeCounter() {
  let counter = 0;

  return function (predicate) {
    counter = predicate(counter);
    return counter;
  };
}

function increase(n) {
  return ++n;
}
function decrease(n) {
  return --n;
}

const counter = makeCounter();
console.log(counter(increase)); // 1
console.log(counter(decrease)); // 0
```

## 24.5 캡슐화와 정보 은닉

캡슐화는 객체의 상태를 나타내는 프로퍼티와 프로퍼티를 참조하고 조작할 수 있는 동작인 메서드를 하나로 묶는 것이다.  
이 캡슐화를 객체의 특정 프로퍼티나 메서드를 감출 목적으로 사용하기도 하는데 이를 정보 은닉이라고 한다.  
이는 결합도를 낮추는 효과가 있다.

```js
const Person = (function () {
  let _age = 0;

  function Person(name, age) {
    this.name = name;
    _age = age;
  }

  Person.prototype.sayHi = function () {
    console.log(`Hi! My name is ${this.name}, ${_age}`);
  };

  return Person;
})();

const me = new Person("Lee", 20);
me.sayHi();
console.log(me.name);
console.log(me._age);

const you = new Person("Kim", 30);
you.sayHi();
console.log(you.name);
console.log(you._age);
```

위 예제에서 \_age 변수를 private 하게 두었다. 다만 sayHi 는 단 한번 생성되는 메서드라서 \_age 변수가 유지가 잘 되지 않는다는 문제가 있다. private 를 사용하여 개선할 수 있다.

## 24.6 자주 발생하는 실수

아래 예제는 var 로 인해 발생한다. 여기서 i 는 전역 변수(프로퍼티)를 참조하고 있어 3 이 출력된다.

```js
var funcs = [];

for (var i = 0; i < 3; i++) {
  funcs[i] = function () {
    return i;
  };
}

for (var j = 0; j < funcs.length; j++) {
  console.log(funcs[j]());
}
```

원하는 동작을 하기 위해서는 블록스코프를 가지는 let 으로만 키워드를 수정해도 되지만 클로저를 쓰자면 아래와 같다.

```js
var funcs = [];

for (var i = 0; i < 3; i++) {
  funcs[i] = (function (id) {
    return function () {
      return id;
    };
  })(i);
}

for (var j = 0; j < funcs.length; j++) {
  console.log(funcs[j]());
}
```

let, const 키워드를 사용하는 반복문은 코드 블록을 반복 실행할 때마다 새로운 렉시컬 환경을 생성하여 반복하는 당시의 상태를 스냅샷처럼 저장한다.  
반복문 블록 내부에 함수가 정의되어 있다면 의미 있지만 없을 경우 참조가 없기 때문에 GC 의 대상이 된다.

위 예제는 아래와 같이 Array 객체를 활요한 고차함수로도 풀 수 있다.

```js
const funcs = Array.from(new Array(3), (_, i) => () => i);
funcs.forEach((f) => console.log(f()));
```

# 🔥 추가학습 필요

- LexicalEnvironment, VariableEnvironment 달라지는 경우
- 자유 변수
