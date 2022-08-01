# 13. 스코프

스코프는 다른 프로그래밍 언어에서도 중요한 개념이며, 자바스크립트에서의 스코프는 다른 프로그래밍 언어와 구별되는 특징이 있으며 키워드에 따라 스코프가 다르게 동작할 수 있으므로 주의할 필요가 있다.

## 13-1. 스코프?

간단히 말하자면 유효범위 이다. 변수, 함수 등을 어느 범위까지 사용할 수 있는지 알 수 있게 해준다. 식별자가 유효한 범위이다. 아무 곳에서 모든 변수 함수 등을 사용할 수 있다면 코드는 엉망진창이 될 것이다.

아래는 매개변수는 함수내에서만 사용가능함을 보여주는 간단한 예제이다. 다만, 외부에 변수 x, y가 있다면 이야기는 달라진다.

```js
function add(x, y) {
  console.log(x, y);
  return x + y;
}

add(1, 2); // 1, 2

console.log(x, y); // ReferenceError: x is not defined
```

다음의 예제를 보자

```js
var var1 = 1;
if (true) {
  var var2 = 2;
  if (true) {
    var var3 = 3;
  }
}

function foo() {
  var var4 = 4;
  function bar() {
    var var5 = 5;
  }
}

console.log(var1); // 1
console.log(var2); // 2
console.log(var3); // 3
console.log(var4); // ReferencError
console.log(var5); // ReferencError
```

블록은 중첩이 가능하고 위 예제는 var 를 사용하여 위와 같이 표기되나 다른 키워드를 사용할 경우 다른 결과가 도출될 것이다.

모든 식별자는 자신이 선언된 위치에 의해 다른 코드가 식별자 자신을 참조할 수 있는 유효범위가 결정되며, 이를 **스코프** 라고 한다.

```js
var x = "global";

function foo() {
  var x = "local";
  console.log(x); // 1 - local
}

console.log(x); // 2 - global
```

위의 예제에서 살펴보자면 같은 x 라는 변수를 사용하고 있으나 다른 값이 출력하고 있다. 이는 자바스크립트 엔진이 **식별자 결정** 을 통해 어떤 변수를 참조할 지를 정한 것이다. 따라서, 스코프란 자바스크립트 엔진이 식별자를 검색할 때 사용하는 규칙이라고도 할 수 있다고 한다.

자바스크립트 엔진이 코드를 실행 할 때는 문맥과 환경을 고려하려 동일한 코드가 다른 결과가 출력된다. ~~23장 참조~~

우리는 당연한 듯이 쓰고 있지만 중요한 부분이라고 생간된다. 위의 예제에서 동일한 이름의 x 라는 변수를 사용하고 있으나 이를 어떻게 구별할 것인가?  
식별자는 값을 구별할 수 있어야 함으로 유일해야 하는데 사용하였는가? 이는 스코프가 네임스페이스 역할을 하기 때문이다.

> 🚨 &nbsp; var 의 중복 허용???  
> let, const 는 같은 스코프에서 중복 선언을 할 경우 오류가 발생한다.  
> 하지만 var 는 아래와 같이 사용할 경우 오류가 나지 않는다.
>
> ```js
> var x = 1;
> var x = 2;
> console.log(x);
> ```
>
> 이는 중복 선언이기는 하지만 값이 재할당 되는 것으로 같은 이름의 식별자가 하나 더 생기는 것이 아니다. 이는 부수효과가 날 수도 있는 위험이 있기 때문에 var 의 사용은 지양 되고 있다.

## 13.2 스코프의 종류

코드는 전역과 지역으로 구별 될 수 있고 스코프도 이를 따라 전역, 지역 스코프가 있다.

<table>
<thead>
<tr>
<th>구분</th>
<th>설명</th>
<th>스코프</th>
<th>변수</th>
</tr>
</thead>
<tbody>
<tr>
<td>전역</td>
<td>코드의 가장 바깥 영역</td>
<td>전역 스코프</td>
<td>전역 변수</td>
</tr>
<tr>
<td>지역</td>
<td>함수 몸체 내부</td>
<td>지역 스코프</td>
<td>지역 변수</td>
</tr>
</tbody>
</table>

### 13.2.1 전역과 지역 스코프

```js
var x = "x";
var y = "y";

function outer() {
  var z = "outer z";

  console.log(x); // "x"
  console.log(y); // "y"
  console.log(z); // "outer z"

  function inner() {
    var x = "inner x";

    console.log(x); // "inner x"
    console.log(y); // "y"
    console.log(z); // "outer z"
  }

  inner();
}

outer();

console.log(x); // "x"
console.log(y); // "y"
console.log(z); // ReferenceError: z is not defined
```

앞선 표의 설명과 같이 전역은 코드의 가장 바깥 영역을 지칭하고 이 전역은 전역 스코프를 만들며 해당 영역에 변수를 선언할 경우 어디서든 참조 할 수 있는 전역 변수가 된다. 위의 예제에서는 최상단의 x, y 가 전역 변수이다.

### 13.2.2 지역과 지역 스코프

지역이란 함수 몸체 내부를 뜻하며 지역은 지역 스코프를 지칭하며 지역 스코프는 자신의 하위 스코프에서 유효하다. 해당 예제에서 outer 의 지역 변수의 경우 inner 함수의 지역 스코프에서 유효하다.

## 13.3 스코프 체인

함수는 전역에서 정의할 수도 있고 함수 내부에서도 중첩으로 생성될 수도 있다. 중첩으로 될 경우 스코프도 중첩되게 되는데 이로인해 계층적 구조를 지니게 된다. 코드의 제일 바깥에는 전역 스코프가 있으므로 모든 지역의 스코프의 최상위 스코프는 전역 스코프이다.

이와 같이 스코프가 계층적으로 연결된 것을 스코프 체인이라고 하며 자바스크립트 엔진은 변수를 참조할 때 변수를 참조하는 코드의 스코프에서 시작하여 스코프 체인을 통해 상위 스코프 방향으로 이동하며 선언된 변수를 검색한다.

스코프 체인은 물리적인 실체가 존재하는데 렉시컬 환경을 생성하여 지니고 있다. 자세한 내용은 23장에서 설명한다고 한다.

### 13.3.1 스코프 체인에 의한 변수 검색

자바스크립트 엔진은 하위 스코프로 내려가면서 식별자를 검색하는 일은 없으므로, 상위 스코프에서 유효한 변수는 하위 스코프에서 참조할 수 있지만 하위 스코프에서 유효한 변수를 상위 스코프에서 참조 할 수 없다.

### 13.3.2 스코프 체인의 의한 함수 검색

```js
function foo() {
  console.log("global foo");
}

function bar() {
  console.log("global bar");
  console.log(foo);

  function foo() {
    console.log("bar - foo");
  }

  var foo = 1;
  console.log(foo);
}

bar();
```

앞절에서 설명한 내용과 다를 바가 없다. 변수가 아닌 함수로 대체되었을 뿐.  
이를 통해 스코프는 `식별자를 검색하는 규칙` 이라고 표현하는 편이 옳다는 것을 나타내고자 하였다.

## 13.4 함수 레벨 스코프

지역은 함수 몸체 내부를 말하고 지역은 지역 스코프를 만든다. 이는 코드 블록이 아닌 함수에 의해서만 지역 스코프가 생성된다는 뜻이며, 코드 블록에 의한 스코프를 블록 레벨 스코프라고 한다. var 키워드는 함수 레벨 스코프만 허용하고 있다.

> 뒷 15장에서 설명하지만 let, const 는 블록 레벨 스코프를 가지며 함수 레벨 스코프를 가지는 이유 또한 var 의 지양 이유 중 하나인 것 같다.

```js
var x = 1;
if (true) {
  var x = 10;
}

console.log(x); // 10

let y = 1;
if (true) {
  let y = 10;
}

console.log(y); // 1
```

## 13.5 렉시컬 스코프

```js
var x = 1;

function foo() {
  var x = 10;
  bar();
}

function bar() {
  console.log(x);
}

foo();
bar();
```

위 예제의 실행 결과는 bar 함수의 상위 스코프가 무엇인지에 따라 결정된다. 아래의 2가지 경우를 에측할 수 있다.

1. **함수를 어디서 `호출`했는지에 따라 함수의 상위 스코프를 결정**
2. **함수를 어디서 `정의`했는지에 따라 함수의 상위 스코프를 결정**

첫 번째는 동적 스코프라고 하며, 두 번째는 렉시컬 스코프(정적 스코프)라고 한다. 자바스크립트를 비롯한 대부분의 언어는 렉시컬 스코프를 따르고 있다.

자바스크립트에서의 상위 스코프는 함수 정의가 실행될 때 정적으로 결정되며, 함수 정의가 실행되어 생성된 함수 객체는 이렇게 결정된 상위 스코프를 기억한다. 함수가 호출될 때마다 상위 스코프를 참조할 필요가 있기 때문이다.

따라서 위의 예제는 1을 두번 출력한다.

# 14. 전역 변수의 문제점

이게 하나의 장으로 나올 꺼라고는 생각지 못했다. 뭐 그만큼 중요하다고 저자도 생각하는게 아닌가 싶다.  
가능한 지역변수를 사용하는 것이 좋다. 전역변수로 등록하여 사용할 경우 추적도 어렵고 예상치 못한 부수효과가 발생할 가능성 등 위험성이 증가하기 때문이다. ☠️  
이에 대해 억제하는 방법을 알아보는 장이다.

## 14.1 변수의 생명 주기

### 14.1.1 지역 변수의 생명 주기

변수는 생성되고 소멸되는 주기가 있다. 전역 변수의 경우는 어플리케이션의 생명주기와 같지만 지역 변수(함수 레벨 스코프)는 함수가 호출되면 생성되고 함수가 종료하면 소멸한다.

```js
function foo() {
  var x = "local";
  console.log(x);
  return x;
}

foo();
console.log(x); // ReferenceError
```

앞 장에서 우리는 스코프에 대해서 배웠기 때문에 당연한 예제지만, 여기에서는 지역 변수의 생명 주기는 함수의 생명 주기와 일치한다는 것을 표현하고자 한다.  
함수가 살아있는 동안은 변수 x도 살아있지만 함수의 스코프를 참조하는 곳이 없게 된다면 x도 사라진다. ~~이는 클로저와 관련이 있다. 아마 뒷 장에 있을 듯 싶다. 🙄~~

```js
var x = "global";

function foo() {
  console.log(x); // undefined
  var x = "local";
  return x;
}

foo();
console.log(x); // global
```

위 예제에서 foo 내부의 x는 호이스팅 되어 undefined 가 호출 된다. 이처럼 호이스팅은 스코프를 단위로 동작하며 변수 선언이 스코프의 선두로 끌어 올려진 것처럼 동작하는 자바스크립트 고유의 특징을 지칭한다.

> 💿 위의 예제에서 let 을 쓴다면??

### 14.1.2 전역 변수의 생명주기

이 책의 예제는 대부분이 var 를 예제로 사용하고 있는데 몇 번 개정되면 let, const 위주로 바꾸지 않을까 싶다. 💩  
전역 코드는 코드가 로드되면 곧바로 해석되고 실행되며 var 키워드로 선언한 전역 변수는 전역 객체의 생명 주기와 일치한다.

> 💿 &nbsp; 전역 객체
> 코드가 실행되기 이전 단계에 자바스크립트 엔진에 의해 어떤 객체보다도 먼저 생성되는 특수한 객체로 클라이언트 사이드 환경에 따라 다양한 식별자가 존재했으나 ES11 부터는 globalThis 로 통일되었다.  
> 전역 객체는 표준 빌트인 객체(Object, String...) 과 환경에 따른 호스트 객체 그리고 var 로 선어한 전역 변수와 전연 함수를 프로퍼티를 가진다.

## 14.2 전역 변수의 문제점

- 암묵적 결합
  - 어디서든 참조하고 할당할 수 있는 변수를 사용하겠다는 것
  - 이는 모든 코드가 전역 변수를 참조하고 변경할 수 잇는 암묵적 결합
  - 변수의 범위가 크면 클수록 가독성은 떨어지고 부수효과의 위험성이 높아짐
- 긴 생명 주기
  - 리소스도 오래 사용하며, 오래동안 존재하는 만큼 의도치 않은 재할당 등 위험성이 높아진다.
- 스코프 체인 상에서 종점에 존재
  - 앞서 배웠던 것 처럼 변수를 검색함에 있어서 현재 스코프에서 최상위 스코프까지 올라간다는 것이다. 속도가 느리다.
- 네임스페이스 오염
  - **자바스크립트는 파일이 분리되어 있다 해도 하나의 전역 스코프를 공유 한다는 것이다.** 이는 예상치 못한 결과를 가져올 수 있다.

## 14.3 전역 변수의 사용을 억제하는 방법

전역 변수를 꼭 사용할 이유를 찾지 못하면 안 쓰는게 좋고 스코프를 작게 사용하는 것이 좋다. 이와 관련된 방법은 다음과 같다.

### 14.3.1 즉시 실행 함수

함수의 정의와 동시에 실행되는 함수로 전역 변수를 제한하는 방법이다.  
라이브러리 등에서 주로 사용한다.

### 14.3.2 네임스페이스 객체

아직까지 용도에 해당하게 직접적으로 사용해본 적은 없다. 중첩하여 계층적으로 구성할 수 있다.  
책에서도 식별자의 충돌을 방지하는 효과는 있으나 객체 자체가 전역변수에 할당 되므로 유용해 보이지 않다고 기술하고 있다. 😳

```js
var MYAPP = {};

MYAPP.type = "application";
console.log(MYAPP.type);

MYAPP.person = {
  name: "John",
  age: 30,
};
console.log(MYAPP.person.name);
```

### 14.3.3 모듈 패턴

자바스크립트의 클로저 기반으로 동작하며 전역 변수의 억제와 캡슐화를 구현할 수 있다.

> 캡슐화: 객체의 상태를 나태내는 프로퍼티와 프로퍼티를 조작하는 동작을 묶은 것  
> 정보 은닉: 객체의 특정 프로퍼티나 메서드를 감추는 것

해당 책에서는 자바스크립트에서는 public, private, protected 등의 접근제한자를 제공하지 않는다고 하는데 작성자가 알기로 클래스의 속성으로 private(#) 는 있다. ~~ES2019 추가~~  
클래스로 구현하지 않고 다음과 같이 구현이 가능하다. 클로저와 연관되어 있으며, 뒷 장에서 설명이 추가 설명이 있을 것 같다.

### 14.3.4 ES6 모듈

ES6의 모듈을 사용하면 더는 전역 변수를 사용할 수 없다. 파일 자체의 독자적인 모듈 스코프를 제공하기 때문이다. 지원 환경은 확인이 필요하나 대부분 지원하며 책에서는 type="module" 과 확장자 .mjs 를 권장하고 있다.

> Webpack 이 언급되나 뒷장에서 설명한다고 기술되어 있다.

# 15. let, const 키워드와 블록 레벨 스코프

## 15.1 var 키워드로 선언한 변수의 문제점

이미 앞선 예제와 정리에서 한번 씩 언급했었던 것 같다.

1. 변수의 중복 선언 허용
2. 함수 레벨 스코프
3. 변수의 호이스팅

위의 3가지 문제점이 있으며 개발자에게 부수효과 및 혼란을 주므로 충분히 문제되는 부분이라고 생각한다.

## 15.2 let 키워드

var 키워드와 차이점을 중심으로 알아보자.

### 15.2.1 변수 중복 선언 금지

할 경우 SyntaxError 가 난다.

### 15.2.2 블록 레벨 스코프

var 키워드로 선언한 변수는 함수 레벨 스코프를 가진다고 앞에서 말하였다. let 의 경우는 블록 레벨의 스코프를 가진다.

> 블록 레벨 스코프: 모든 코드 블록에 지역 스코프를 인정(if, for, try catch...)

```js
let foo = 1;

{
  let foo = 2;
  console.log(foo); // 2
  let bar = 3;
}

console.log(foo); // 1
console.log(bar); // ReferenceError
```

### 15.2.3 변수 호이스팅

var 키워드는 선언과 초기화를 같이 진행한다. 하지만 let 키워드는 그렇지 않다.
선언이 먼저 되기는 하지만 초기화는 실제 해당하는 코드에 도착해야 이뤄진다.

```js
console.log(foo); // ReferenceError

let foo;
console.log(foo); // undefined

foo - 1;
console.log(foo); // 1
```

var 와 선언은 호이스팅이 되어 선언은 되지만 초기화는 되지 않는 것이다. 이 선언 후 초기화 되지 않아 참조할 수 없는 구간을 일시적 사각지대(TDZ) 라고 한다.

> 💿 &nbsp; 자바스크립트는 ES6에 도입된 let, const 를 포함하여 모든 선언에서 호이스팅을 한다. 다만 let, const, class 를 사용한 선언은 호이스팅을 하지 않은 것 처럼 동작한다.

## 15.2.4 전역 객체와 let

var 키워드로 선언한 전역 식별자는 전역 객체의 프로퍼티가 되었으나 let 의 키워드로 생성할 경우 그렇지 않다.
~~자세한 내용은 23장에서 설명한다.~~

```js
var y = 2;
console.log(window.y); // 2
console.log(y); // 2

let x = 1;

console.log(window.x); // undefined
console.log(x); // 1
```

## 15.3 const 키워드

일반적으로 상수를 선언하기 위해 사용하며 대부분은 let 키워드와 동일하다.

### 15.3.1 선언과 초기화

const 키워드는 선언과 동시에 초기화를 해야한다.

```js
const foo;  // SyntaxError: Missing initializer in const declaration
```

### 15.3.2 재할당 금지

const 키워드로 선언한 변수는 재할당이 금지 된다.

### 15.3.3 상수

재할당이 금지된 변수를 지칭하며 상수라고도 한다. 일반적으로 대문자와 언더스코어로 많이 표기하며 고정값을 표기할 때 사용한다.

### 15.3.4 const 키워드와 정체

const 키워드로 선언된 변수에 객체를 할당한 경우 값을 변경할 수 있다. const 키워드는 재할당을 금지할 뿐 불변을 의미하지 않기 때문이다.

## 15.4 var vs let vs const

책에서는 기본적으로 const 를 권장하고 변경이 필요할 경우 let 을 권장한다.  
아래와 같은 사양들이 권장된다.

- ES6 사용한다면 레거시를 마이그레서이션 해봄
- 변수의 스코프는 최대한 작게

# 16. 프로퍼티 어브리뷰트

## 16.1 내부 슬롯과 내부 메서드

이 장에서 설명하는 프로퍼티 어트리뷰트를 이해하기 위해서는 내부 슬롯과 내부 메서드의 개념을 알아야 한다.  
이는 자바스크립트 엔진의 구현 알고리즘을 설명하기 위해 ECMAScript 사양에서 사용하는 의사 프로퍼티와 의사 메서드이며 사양 문서에서 이중 대괄호로 감싸져 표현된다.

> [[GetPrototypeOf]]...

일반적으로 내부 슬롯과 내부 메서드는 외부에서 접근하도록 공개된 프로퍼티가 아니다. 하지만 일부 내부 슬롯과 내부 메서드에 한하여 간접적으로 접근할 수 있는 수단을 제공한다.

> 💿 &nbsp; 모든 객체는 [[Prototype]] 이라는 내부 슬롯을 가지고 이 슬롯의 경우 `__proto__` 통해 간접적 접근이 가능하다.

```js
const obj = {};

obj.[[Prototype]];
obj.__proto__; // {}
```

## 16.2 프로퍼티 속성과 프로퍼티 디스크립터 객체

자바스크립트 엔진은 프로퍼티를 생성할 때 프로퍼티의 상태를 나타내는 프로퍼티 속성을 기본 값을 자동 정의한다.  
프로퍼티의 상태란 `값, 값의 갱신 가능 여부, 열거 가능 여부, 재정의 가능 여부` 를 나타낸다.  
이러한 프로퍼티 속성은 자바스크립트 엔진이 관리하는 내부 상태 값 내부 슬롯이다. 따라서 프로퍼티 속성에 직접 접근을 할 수는 없지만 Object.getOwnPropertyDescriptor() 메서드를 사용하여 프로퍼티 디스크립터 객체를 반환 받을 수 있다.

```js
const obj = {
  type: "object",
};

console.log(Object.getOwnPropertyDescriptor(obj, "type"));
// {value: 'object', writable: true, enumerable: true, configurable: true}
```

해당 메서드의 자세한 명세는 MDN 등 다른 문서를 참조하자

ES8에 도입된 Object.getOwnPropertyDescriptors() 메서드는 객체에서 가지고 있는 모든 프로퍼티에 대한 디스크립터 객체를 반환한다.

```js
const obj = {
  type: "object",
  label: "test",
};

console.log(Object.getOwnPropertyDescriptors(obj));

// {type: {value: 'object', writable: true, enumerable: true, configurable: true}
// label: {value: 'test', writable: true, enumerable: true, configurable: true}}
```

## 16.3 데이터 프로퍼티와 접근자 프로퍼티

- 데이터 프로퍼티: 키와 값으로 구성된 일반적인 프로퍼티
- 접근자 프로퍼티: 자체적으로는 값을 갖지 않고 데이터 프로퍼티의 값을 읽거나 저장할 때 호출되는 접근자 함수로 구성된 프로퍼티

### 16.3.1 데이터 프로퍼티

<table>
<thead>
<tr>
<th>프로퍼티 속성</th>
<th>프로퍼티 디스크립터 객체의 프로퍼티</th>
<th>설명</th>
</tr>
</thead>
<tbody>
<tr>
<td>[[Value]]</td>
<td>value</td>
<td>프로퍼티 키를 통해 값에 접근하는 반환되는 값</td>
</tr>
<tr>
<td>[[Writable]]</td>
<td>value</td>
<td>값의 변경 가능 여부를 나태내며 불리언 값을 가짐</td>
</tr>
<tr>
<td>[[Enumerable]]</td>
<td>value</td>
<td>프로퍼티의 열거 가능 여부</td>
</tr>
<tr>
<td>[[Configurable]]</td>
<td>value</td>
<td>프로퍼티의 재정 가능 여부 확인 가능 여부</td>
</tr>
</tbody>
</table>

### 16.3.2 접근자 프로퍼티

값을 지니지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수로 구성됨

<table>
<thead>
<tr>
<th>프로퍼티 속성</th>
<th>프로퍼티 디스크립터 객체의 프로퍼티</th>
<th>설명</th>
</tr>
</thead>
<tbody>
<tr>
<td>[[Get]]</td>
<td>get</td>
<td>데이터 프로퍼티의 값을 읽을 때 호출</td>
</tr>
<tr>
<td>[[Set]]</td>
<td>set</td>
<td>데이터 프로퍼티의 값을 저장할 때 호출</td>
</tr>
<tr>
<td>[[Enumerable]]</td>
<td>value</td>
<td>프로퍼티의 열거 가능 여부</td>
</tr>
<tr>
<td>[[Configurable]]</td>
<td>value</td>
<td>프로퍼티의 재정 가능 여부 확인 가능 여부</td>
</tr>
</tbody>
</table>

```js
const person = {
  _name: "John",
  _age: 30,
  get name() {
    return this._name;
  },
  set name(value) {
    this._name = value;
  },
};

console.log(person._name); // John
console.log(person.name); // John

console.log(Object.getOwnPropertyDescriptor(person, "_name"));
console.log(Object.getOwnPropertyDescriptor(person, "name"));

// {value: 'John', writable: true, enumerable: true, configurable: true}
// {enumerable: true, configurable: true, get: ƒ, set: ƒ}
```

위 코드에서 접근자 함수에 접근하는 단계는 다음과 같다.

1. 프로퍼티 키가 유효한지 확인
2. 프로토타입 체인에서 프로퍼티 검색
3. 데이터 프로퍼티 또는 접근자 프로퍼티인지 확인
4. 접근자 프로퍼티의 프로퍼티 속성에 맞는 함수 호출 및 값을 가져간다.

> 프로토타입 19장 참조

## 16.4 프로퍼티 정의

Object.defineProperty 메서드를 통해 새로운 프로퍼티를 추가하며 속성을 명시적으로 정의하거나 기존의 속성을 재정의 할 수 있다.

> 관련한 예제는 생략하였다.

한 번에 정의하는 방법으로는 Object.defineProperties 가 있고 두 메서드로 생성하면서 각 설정을 빈값으로 둘 경우 falsy 값을 디폴트로 지니게 된다.

## 16.5 객체 변경 방지

객체의 변경을 방지하는 메소드

<table>
<thead>
<tr>
<th>구분</th>
<th>메서드</th>
<th>프로퍼티 추가</th>
<th>프로퍼티 삭제</th>
<th>프로퍼티 값 읽기</th>
<th>프로퍼티 값 쓰기</th>
<th>프로퍼티 속성 재정의</th>
</tr>
</thead>
<tbody>
<tr>
<td>객체 확장 금지</td>
<td>Object.preventExtensions</td>
<td>X</td>
<td>O</td>
<td>O</td>
<td>O</td>
<td>O</td>
</tr>
<tr>
<td>객체 밀봉</td>
<td>Object.seal</td>
<td>X</td>
<td>X</td>
<td>O</td>
<td>O</td>
<td>X</td>
</tr>
<tr>
<td>객체 동결</td>
<td>Object.freeze</td>
<td>X</td>
<td>X</td>
<td>O</td>
<td>X</td>
<td>X</td>
</tr>
</tbody>
</table>

### 16.5.1 객체 확장 금지

Object.preventExtensions 메서드는 객체의 확장을 금지한다.  
Object.isExtensible 메서드로 확장 가능 여부를 확인 할 수 있다.  
앞의 테이블에 설명이 있지만 확장만 불가능하고 삭제는 가능하다.

```js
const obj = {
  name: "John",
};
console.log(Object.isExtensible(obj)); // false

Object.preventExtensions(obj);
console.log(Object.isExtensible(obj)); // true

delete obj.name;
console.log(obj.name); // undefined
```

### 16.5.2 객체 밀봉

Object.seal 메서드를 사용한 객체는 읽기와 쓰기만 가능하며 Object.isSealed 메서드로 밀봉 여부를 확인할 수 있다.

```js
const obj = {
  name: "John",
};
console.log(Object.isSealed(obj)); // false

Object.seal(obj);
console.log(Object.isSealed(obj)); // true

delete obj.name;
obj.name = "Jame";
console.log(obj.name); // Jame
```

### 16.5.3 객체 동결

Object.freeze 메서드는 객체를 읽기만 가능한 상태로 동결 시키며 Object.isFrozen 메서드로 동결 여부를 확인할 수 있다.

```js
const obj = {
  name: "John",
};
console.log(Object.isFrozen(obj)); // false

Object.freeze(obj);
console.log(Object.isFrozen(obj)); // true

delete obj.name;
obj.name = "Jame";
console.log(obj.name); // John
```

### 16.5.4 불변객체

앞선 변경 방지 메서드들은 얕은 변경 방지이다. 중첩 객체의 경우에는 영향을 주지 못하며 재귀적으로 중첩 객체까지 동결 시킬 필요가 있다.

# 17. 생성자 함수에 의한 객체 생성

여지껏 객체 리터럴로 객체를 생성하였지만 객체를 생성하는 방법은 다양하다.

## 17.1 Object 생성자 함수

new 연산자와 Object 생성자 함수를 호출하면 빅 객체를 생성하여 반환하지만 생성한 이후 프로퍼티 또는 메서드를 추가하여 객체를 완성해야 한다.  
빌트인 생성자 함수가 있다. (String, Number, Boolean...)  
객체 리터럴을 사용하는 것이 일반적으로 더 편한다.

```js
const person = new Object();

person.name = "John";
person.age = 30;

const strObj = new String("Hello");
const numObj = new Number(10);
const boolObj = new Boolean(true);
...
```

## 17.2 생성자 함수

### 17.2.1 객체 리터럴에 의한 객체 생성 방식의 문제

객체의 구조가 같을 때 수십 객체를 생성해야 한다면 시간이 오래 걸린다.

### 17.2.2 생성자 함수에 의한 객체 생성 방식의 장점

객체의 구조를 본 뜬 생성자 함수를 구현하여 여러개의 객체를 간편하게 만들 수 있다.

```js
function Person(name, age) {
  this.name = name;
  this.age = age;
}

const person1 = new Person("John", 30);
const person2 = new Person("Jame", 20);
```

> 💿 &nbsp; this는 객체 자신의 프로퍼티나 메서를 참조하기 위한 자기 참조 변수이다. this 바인딩은 하뭇 호출 방식에 따라 동적으로 결정된다. 22 장에서 this 에 대해 자세히 다룬다.

<table>
<thead>
<tr>
<th>함수 호출 방식</th>
<th>this 바인딩</th>
</tr>
</thead>
<tbody>
<tr>
<td>일반 함수로서 호출</td>
<td>전역</td>
</tr>
<tr>
<td>메서드로서 호출</td>
<td>메서드를 호출한 객체</td>
</tr>
</tr>
<tr>
<td>생성자 함수로서 호출</td>
<td>생성자 함수가 생성할 인스턴스</td>
</tr>
</tbody>
</table>

### 17.2.3 생성자 함수의 인스턴스 생성 과정

인스턴스 생성 및 인스턴스 초기화를 하며 new 연산자와 함께 호출하면 암묵적으로 인스턴스를 생성하고 인스턴스를 초기화한 후 인스턴스를 반환한다. 과정은 아래와 같다.

1. 인스턴스 생성과 this 바인딩
2. 인스턴스 초기화
3. 인스턴스 반환

```js
// 생성자 함수
function Circle(radius) {
  // 1. 암묵적 빈 객체가 생성되고 this 에 바인딩

  // 2. this 에 바인딩되어 있는 인스턴스를 초기화
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };

  // 3. 완성된 인스턴스가 바인딩된 this 를 암묵적 반환
}

// 인스턴스 생성
const circle = new Circle(10);
console.log(circle);
```

생성자 함수에서 다른 객체를 명시적으로 반환한다면 명시적으로 반환하는 객체가 반환되지만 원시값을 반환할 경우 무시되며 암묵적으로 this 가 반환된다.

```js
function Circle1(radius) {
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };

  return {};
}

function Circle2(radius) {
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };

  return 100;
}

const circle1 = new Circle1(10);
const circle2 = new Circle2(10);
console.log(circle1); // {}
console.log(circle2); // Circle2 {radius: 10, getDiameter: ƒ}
```

생성자 함수에서 명시적으로 this 가 아닌 값을 반환하는 것을 기본 동작을 훼손하므로 생략해야 한다.

### 17.2.4 내부 메서드 [[call]], [[construct]]

함수 선언문 또는 함수 표현식으로 정의한 함수는 일반적인 함수로서 호출할 수 있고, 생성자 함수로서 호출할 수 있다.  
함수 객체는 객체이므로 일반 객체와 동일하게 동작할 수 있고 추가적으로 일반 객체와 다르게 호출이 가능하다.  
함수로서 동작하기 위해 `[[Environment]]`, `[[FormalParameters]]` 등의 내부슬롯과 `[[Call]]`, `[[Construct]]` 등의 내부 메서드를 추가로 가지고 있다.

일반 함수로 호출할 경우 함수 객체 내부의 `[[Call]]` 메서드를 호출하고, 생성자 함수로 호출할 경우 생성자 함수 내부의 `[[Construct]]` 메서드를 호출한다.

```js
function foo() {}

// 일반적인 함수로서 호출: [[call]] 호출
foo();

// 생성자 함수로서 호출: [[construct]] 호출
new foo();
```

[[call]] 메서드를 가지는 함수 객체를 `callable` 라 하며, [[construct]] 메서드를 가지는 함수 객체를 `constructor`, 없을 경우 `non-constructor` 라 한다.

모든 함수 객체는 callable 이지만 constructor 일수도 있고 아닐 수도 있다.

### 17.2.5 constructor 와 non-constructor 구분

자바스크립트 엔진은 함수 정의를 평가하여 함수 객체를 생성할 때 함수 정의 방식에 따라 함수를 constructor 또는 non-constructor 로 구분한다.

- constructor: 함수 선언문, 함수 표현식, 클래스
- non-constructor: 메서드, 화살표 함수

주의할 것은 ECMAScript 사양에서 메서드로 인정하는 범위가 일반적인 의미의 메서드보다 좁다는 것이다.

```js
function foo() {}
const bar = function () {};
const baz = {
  x: function () {},
};

new foo(); // constructor
new bar(); // constructor
new baz.x(); // constructor

const arrow = () => {};
new arrow(); // non-constructor

const obj = {
  x() {},
};
new obj.x(); // non-constructor
```

함수를 프로퍼티 값으로 사용하면 일반적으로 메서드라고 하지만 ECMAScript 사양에서는 ES6의 메서드 축약 표현만을 의미한다.  
non-constructor 함수는 [[Construct]] 메서드를 가지지 않으며 생성자 함수를 호출하면 에러가 발생한다.

### 17.2.6 new 연산자

new 연산자로 함수를 호출하면 해당 함수는 생성자 함수로 동작을 하며, 해당 함수가 constructor 이어야 한다.

```js
function add(x, y) {
  console.log(this);
  return x + y;
}

let inst = new add();
// 함수가 객체를 반환하지 않으므로 this 가 반환되었다?
console.log(inst); // add {}

function createUser(name, age) {
  return {
    name,
    age,
  };
}

inst = new createUser("John", 30);
console.log(inst); // { name: 'John', age: 30 }
```

같은 이야기가 반복되는데 new 연산자 없이 하는 일반 호출의 경우 일반 함수로 호출이 된다.

```js
function Circle(radius) {
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

const circle = Circle(5);
console.log(circle); // undefined
// 일반 함수의 this 는 전역 객체이다.
console.log(radius); // 5
console.log(getDiameter()); // 10

circle.getDiameter(); // TypeError
```

### 17.2.7 new.target

생성자 함수가 new 연산자 없이 호출되는 것을 방지하기 위해 ES6 에서는 메타 프로퍼티라고 불리는 new.target 을 지원한다.

> 🚨 IE는 지원하지 않는다.

new 연산자와 함께 생성자 함수로서 호출되면 함수 내부의 new.target은 함수 자신을 가리키며, new 연산자 없이 일반 함수로 호출된 함수 내부의 new.target은 undefined이다.

```js
function Circle(radius) {
  if (!new.target) {
    return new Circle(radius);
  }

  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

const circle = Circle(5);
console.log(circle.getDiameter()); // 10
```

> 💿 &nbsp; 스코프 세이프 생성자 패턴
> new.target을 지원하지 않는 상황이라면 아래와 같은 스코프 세이프 패턴으로 사용할 수 있다.
>
> ```js
> function Circle(radius) {
>   if (!(this instanceof Circle)) {
>     return new Circle(radius);
>   }
>
>   this.radius = radius;
>   this.getDiameter = function () {
>     return 2 * this.radius;
>   };
> }
>
> const circle = Circle(5);
> console.log(circle.getDiameter()); // 10
> ```
>
> new 연산자와 함계 생성자 함수에 의해 생성된 객체는 프로토타입에 의해 생성자 함수와 연결되며 이 점을 이용하여 new 연산자와 함께 호출되었는지 알 수 있다.

대부분의 빌트인 생성자 함수의 경우 new 연산자와 함께 호출되었는지를 확인한 후 적절한 값을 반환한다.  
Object, Function 생성자 함수는 new 연산자 없이 호출해도 동일하게 동작하지만 String, Number, Boolean 의 경우는 new 연산자 없이 호출할 경우 문자열, 숫자, 불리언 값을 반환한다. 이를 통해 타입 변환에 사용되기도 한다.

```js
let obj = new Object();
console.log(obj); // {}
obj = Object();
console.log(obj); // {}

let str = new String(111);
console.log(str); // String {'111'}
str = String(111);
console.log(str); // "111"
```

# 18. 함수와 일급 객체

## 18.1 일급 객체

다음의 조건을 만족하는 객체를 **일급 객체**라 한다.

- 무명의 리터럴로 생성할 수 있다. 즉, 런타임에 생성이 가능하다.
- 변수나 자료구조에 저장할 수 있다.
- 함수의 매개변수에 전달할 수 있다.
- 함수의 반환값으로 사용할 수 있다.

자바스크립트에서 함수는 위 조건을 모두 만족하므로 일급객체이다.

```js
// 1
const increase = function (num) {
  return ++num;
};

// 2
const a = increase;

// 3, 4
function makeIncrease(a) {
  let num = 0;

  return function () {
    return a(num);
  };
}

makeIncrease(increase)(); // 1
```

## 18.2 함수 객체의 프로퍼티

함수는 객체이므로 프로퍼티를 가질 수 있다.

```js
function square(num) {
  return num * num;
}

console.log(Object.getOwnPropertyDescriptors(square));
/*
arguments: {value: null, writable: false, enumerable: false, configurable: false}
caller: {value: null, writable: false, enumerable: false, configurable: false}
length: {value: 1, writable: false, enumerable: false, configurable: true}
name: {value: 'square', writable: false, enumerable: false, configurable: true}
prototype: {value: {…}, writable: true, enumerable: false, configurable: false}
[[Prototype]]: Object
*/

console.log(Object.getOwnPropertyDescriptor(square, "__proto__")); // undefined
```

`__proto__ ` 는 square 함수의 프로퍼티가 아니고 객체의 접근자 프로퍼티이기 때문에 undefined를 반환한다. ~~자세한 이야기는 19장에서..~~

### 18.2.1 arguments 프로퍼티

arguments 객체로 함수 호출 시 전달된 인수들의 정보를 담고 있는 순회 가능한 유사 배열 객체이며, 함수 내부에서 지역 변수처럼 사용된다.

`??` 함수 객체의 arguments 프로퍼티는 현재 일부 브라우저에서 지원하고 있지만 ES3 부터 폐지되었다. 따라서 Function.arguments 와 같은 접근은 권장되지 않고 있으며 함수 내부에서 지역 변수처럼 사용할 수 있는 arguments 객체를 참조하도록 한다.

자바스크립트는 매개변수와 인수의 개수가 일치하는지 신경쓰지 않는다. 적게 전달 된 경우 기본값을 설정하지 않은 경우 undefined 처리가 되며, 초과된 인수는 무시된다.  
모든 인수는 순서대로 암묵적으로 arguments 객체의 프로퍼티로 저장된다.

> 💿 &nbsp; arguments 객체의 프로퍼티  
> Symbol(Symbol.interator) 프로퍼티는 순회가능한 자료구조인 이터러블로 만들기 위한 프로퍼티이며, length 프로퍼티는 인수의 갯수를 나타낸다.

이러한 arguments 객체는 가변 인자 함수를 구현할 때 유용하다.

```js
function sum() {
  return Array.from(arguments).reduce((acc, num) => acc + num, 0);
}

console.log(sum(1, 2, 3)); // 6
console.log(sum(1, 2, 3, 5, 6, 7, 8, 9, 10)); // 51
```

arguments 객체는 배열 형태로 인자 정보를 담고 있지만 실제 배열이 아닌 유사 배열 객체로 length 프로퍼티를 가진 객체이다.

> 유사 배열 과 이터러블에 대해서 언급되나 뒷 장에서 나올 것이기 때문에 생략하겠다.

ES6에서는 Rest 파라미터가 도입되며 가변 인자 함수를 구현할 때 arguments 를 쓸 필요는 없게 되었지만 알고 있다면 도움이 될 것이다.

### 18.2.2 caller 프로퍼티

ECMAScript 에 포함되지 않고 포함될 예정도 없는 비표준 프로퍼티이다.  
함수 자신을 호출한 함수를 가르키며 실행되는 환경에 따라 다른 결과가 나올 수 있다.  
아래 예제는 브라우저에서 실행한 결과이다.

```js
function foo(func) {
  return func();
}

function bar() {
  return `caller : ${bar.caller}`;
}

console.log(foo(bar)); // caller : function foo..
console.log(bar()); // caller : null
```

### 18.2.3 length 프로퍼티

함수를 정의할 때 선언한 매개변수의 개수를 나타낸다.

```js
function sum(a, b) {
  return a + b;
}
console.log(sum.length); // 2

function foo() {
  return arguments.length;
}
console.log(foo.length); // 0
```

### 18.2.4 name 프로퍼티

ES6 이전까지는 비표준이었으나 ES6에 표준이 되었으며, 함수의 이름을 나타낸다.  
ES5와 ES6 에서 동작을 달리 하는 것을 주의할 필요가 있다.

```js
const namedFunc = function foo() {};
console.log(namedFunc.name); // foo

const anonymousFunc = function () {};
console.log(anonymousFunc.name); // anonymousFunc
```

### 18.2.5 \_\_proto\_\_ 프로퍼티

모든 객체는 [[Prototype]] 이라는 내부 슬롯을 갖고 `__prototype__` 프로퍼티는 해당 내부 슬롯에 접근하기 위해 사용하는 접근자 프로퍼티이다.

```js
const obj = {
  a: 1,
};

console.log(obj.__proto__ === Object.prototype); // true
console.log(Object.hasOwnProperty("__proto__")); // false
console.log(Object.hasOwnProperty("a")); // true `??`
```

### 18.2.6 prototype 프로퍼티

생성자 함수로 호출할 수 있는 함수 객체, 즉 constructor 만이 소유하는 프로퍼티다.  
non-constructor 의 경우 가지고 있지 않다.

```js
(function () {}.hasOwnProperty("prototype")); // true
({}.hasOwnProperty("prototype")); // false
```
