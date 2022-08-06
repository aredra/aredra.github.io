# 19. 프로토타입

자바스크립트는 앞장에서 설명했다시피 다양한 프로그래밍 패러다임을 지원하는 멀티 패러다임 프로그래밍 언어이다.  
일반적인 객체지향 프로그래밍 언어의 특징인 클래스와 상속, 캡슐화를 위한 키워드(public, private, protected) 등이 없어서 객체지향 언어가 아니라고 오해할 수 있지만 프로토타입 기반의 객체지향 프로그래밍 언어이다.  
자바스크립트는 객체 기반 프로그래밍을 지원하는 언어이며 자바스크립트를 이루고 있는 거의 모든 것은 객체이다.(원시값 제외)

> 💿 &nbsp; 클래스  
> ES6에서 클래스가 도입되었으나 이 클래스 역시 함수이며 기존 프로토타입 기반 패턴의 syntactic sugar 라고 볼 수 있다.
> 클래스는 생성자 함수보다 엄격하며 더 많은 기능을 제공한다.

## 19.1 객체지향 프로그래밍

프로그램을 여러 개의 독립적인 단위, 객체의 집합으로 프로그램을 표현하려는 프로그래밍 패러다임을 지칭한다.  
실세계의 실체를 인식하는 철학적 사고를 프로그래밍에 접목하려는 시도에서 시작되었으며, 실체가 가지는 속성을 지녔으며 이를 통해 실체를 인식할 수 있다.  
다양한 속성 중 프로그램에 필요한 속성만 간추려 내어 표현하는 것을 추상화라고 한다.

```js
// 이름, 나이, 취미 속성을 가진 객체
const person = {
  name: "John",
  age: 30,
  hobbies: ["Sports", "Cooking"],
};
console.log(person);
```

프로그래머(주체, Subject)는 이름, 나이 등의 속성을 통해 객체 person 을 다른 객체와 구별할 수 있다. 이와 같이 속성을 통해 여러 개의 값을 하나의 단위로 구성한 복합적인 자료구조를 객체라고 하며, **독립적인 객체의 집합으로 프로그램을 표현하려는 프로그래밍 패러다임이 객체지향 프로그래밍**이다.

아래의 예제를 보자

```js
const circle = {
  radius: 5,
  getDiameter() {
    return this.radius * 2;
  },
  getPerimeter() {
    return this.radius * Math.PI * 2;
  },
  getArea() {
    return this.radius ** 2 * Math.PI;
  },
};

console.log(circle);
console.log(circle.getDiameter());
console.log(circle.getPerimeter());
console.log(circle.getArea());
```

실세계의 원의 개념을 표현한 객체로 반지름은 상태를 나타내는 속성이며, 지름과 원의 둘레, 면적을 구하는 동작 속성을 지니고 있다.  
객체는 상태 데이터와 동작을 하나의 논리적인 단위로 묶은 복합적인 자료구조라고 할 수 있으며 상태 데이터를 `프로퍼티`, 동작을 `메서드` 라고 부른다.

## 19.2 상속과 프로토타입

상속은 객체지향 프로그래밍의 핵심 개념으로 어떤 객체의 프로퍼티와 메서드를 상속받아 불필요한 중복을 제거할 수 있으며, 이러한 재사용은 개발 비용을 현저히 줄일 수 있다.  
자바스크립트에서는 프로토타입을 기반으로 상속을 구현한다.

```js
function Circle(radius) {
  this.radius = radius;
  this.getArea() {
    return Math.PI * this.radius ** 2;
  }
}

const circle1 = new Circle(1);
const circle2 = new Circle(2);

// getArea 메서드 내용이 동일하지만 각 인스턴스마다 새로 생성된다.
// 모든 인스턴스가 공유해서 사용하는 것이 바람직하다.
console.log(circle1.getArea === circle2.getArea); // false
console.log(circle1.getArea());
console.log(circle2.getArea());
```

위 예제에 이미 적어두었지만 Circle 객체에서 getArea 메서드는 어느 인스턴스에서나 동일한 동일한 내용을 갖는다 (동일한 내용의 함수를 가지고 있고 radius 의 경우는 받는 값에 따라 다르다)  
이렇게 동일한 코드를 모든 인스턴스가 중복 소유하는 것은 메모리를 낭비하며 퍼포먼스에 악영향을 주기에 좋지 못하다.

아래와 같이 자바스크립트는 프로토타입을 기반으로 상속을 구현할 수 있다.

```js
function Circle(radius) {
  this.radius = radius;
}
// Circle 인스턴스가 공유할 수 있도록 프로토타입에 추가한다.
Circle.prototype.getArea = function () {
  return Math.PI * this.radius ** 2;
};

const circle1 = new Circle(1);
const circle2 = new Circle(2);

console.log(circle1.getArea === circle2.getArea); // true
console.log(circle1.getArea());
console.log(circle2.getArea());
```

Circle 생성자 함수로 생성된 모든 인스턴스는 자신의 프로토타입, 상위 객체 역할을 하는 Circle의 프로토타입의 모든 프로퍼티와 메서드를 상속받으므로, getArea 메서드를 공유하여 사용할 수 있다.

## 19.3 프로토타입 객체

프로토타입은 객체지향의 프로그래밍의 근간인 상속을 구현하기 위해 사용되며, 어떤 객체의 상위(부모) 객체의 역할을 하는 객체로서 다른 객체 공유 프로퍼티, 메서드를 제공한다.  
모든 객체는 [[Prototype]] 라는 내부 슬롯을 가지며, 이 슬롯의 값은 프로토타입의 참조다.(null 일 수도 있다)  
해당 슬롯에 저장되는 값은 객체 생성 박식에 의해 결정이 된다. [[Prototype]] 내부슬롯에 직접 접근할 수 없지만 `__proto__` 를 통해서 간접적 접근이 가능하다.  
프로토타입은 자신의 constructor 프로퍼티를 통해 생성자 함수에 접근이 가능하며 생성자 함수는 prototype 프로퍼티를 통해 프로토타입에 접근할 수 있다.

### 19.3.1 \_\_proto\_\_ 접근자 프로퍼티

모든 객체는 `__proto__` 접근자 프로퍼티를 통해 자신의 프로토타입, [[Prototype] 내부 슬롯에 간접적 접근할 수 있다.

#### \_\_proto\_\_는 접근자 프로퍼티이다.

앞선 16장에서 언급하였듯 내부 슬롯은 프로퍼티가 이니다. 자바스크립트에서는 원칙적으로느 이러한 내부 슬롯과 내부 메서드에 직접 접근하거나 호출하는 방식을 제공하지 않지만 접근자 프로퍼티를 통해 간접적으로 접근할 수 있게 도와준다.  
`__proto__` 는 접근자 함수 getter/setter 를 통해 [[Prototype]] 내부 슬롯의 값을 가져오거나 변경할 수 있다.

```js
const obj = {};
const parent = { x: 1 };

console.log(obj.__proto__); // {constructor: ƒ, __defineGetter__: ƒ, __defineSetter__: ƒ, hasOwnProperty: ƒ, __lookupGetter__: ƒ, …}
obj.__proto__ = parent;
console.log(obj.x); // 1
```

#### \_\_proto\_\_는 접근자 프로퍼티는 상속을 통해 사용된다.

`__proto__` 접근자 프로퍼티는 객체가 직접 소유하는 프로퍼티가 아닌 Object.prototype 의 프로퍼티로 모든 객체는 상속을 통해 Object.prototype.\_\_proto\_\_ 를 접근자 프로퍼티로 사용할 수 있게 된다.

```js
const person = {
  name: "John",
  age: 30,
  getName() {
    return this.name;
  },
};

console.log(person.hasOwnProperty("__proto__")); // false
console.log({}.__proto__ === Object.prototype); // true
```

#### \_\_proto\_\_ 접근자 프로퍼티를 통해 프로토타입에 접근하는 이유

프로토타입에 접근하기 위해 접근자 프로퍼티를 사용하는 이유는 상호 참조에 의해 프로토타입 체인이 생성되는 것을 방지하기 위해서이다.~~프로토타입 체인은 뒤에서 다룰 예정이다.~~

```js
const parent = {};
const child = {};

child.__proto__ = parent;
parent.__proto__ = child; // TypeError: Cyclic __proto__ value
```

위의 예제가 에러가 발생하는 이유는 두 객체가 서로가 자신의 프로토타입이 되는 비정상적인 프로토타입 체인이 만들어지기 때문이다. 프로토타입 체인은 단방향 링크드 리스트로 구현이 되어야 프로토타입 체인에서 프로토타입을 검색 할 때 무한 루프에 빠지지 않고 검색을 할 수 있다.  
이러한 오류를 막기 위해 \_\_proto\_\_ 접근자 프로퍼티를 통해 프로토타입에 접근하고 교체하도록 구현되어 있다.

#### \_\_proto\_\_ 접근자 프로퍼티를 코드 내에서 직접 사용하는 것은 권장하지 않는다.

\_\_proto** 는 ES5까지는 비표준이었으나 일부 브라우저에서 지원하고 있었기에 호환성을 고려하여 ES6 에서는 표준으로 채택되었다.  
하지만 코드 내에서 직접 사용하는 것은 권장하지 않는다. 모든 객체가 \_\_proto** 접근자 프로퍼티를 사용할 수 있는 것이 아니기 때문이다.~~자세한 내용은 뒤에 언급된다.~~  
때문에 Object.getPrototypeOf() 와 Object.setPrototypeOf() 메서드를 사용하여 프로토타입을 제어하는 것을 권장한다.

```js
// obj 는 프로토타입 체인의 종점이므로 Object.__proto__ 를 상속받을 수 없다.
const obj = Object.create(null);
console.log(obj.__proto__); // undefined
console.log(Object.getPrototypeOf(obj)); // null

const parent = { x: 1 };
Object.setPrototypeOf(obj, parent);
console.log(obj.x); // 1
```

### 19.3.2 함수 객체의 prototype 프로퍼티

함수 객체만이 소유하는 prototype 프로퍼티는 생성자 함수가 생성할 인스턴스의 프로토타입을 가리킨다. 일반 객체는 prototype 프로퍼티를 가지고 있지 않다.  
생성자 함수가 생성할 객체의 프로토타입을 가리키기 때문에 non-constructor 함수는 prototype 프로퍼티를 가지고 있지 않고, 프로토타입도 생성하지 않는다.

```js
(function () {}.hasOwnProperty("prototype")); // true;
({}.hasOwnProperty("prototype")); // false;

const Person = (name) => {
  this.name = name;
};
console.log(Person.hasOwnProperty("prototype")); // false
console.log(Person.prototype); // undefined

const obj = {
  foo() {
    console.log(this);
  },
};
console.log(obj.foo.hasOwnProperty("prototype")); // false
console.log(obj.foo.prototype); // undefined
```

모든 객체가 가지고 있는 (Obejct.prototype 으로부터 상속 받은) \_\_proto\_\_ 접근자 프로퍼티와 함수 객체만이 가지고 있는 prototype 프로퍼티는 동일한 프로퍼티를 가리키지만 **사용하는 주체**가 다르다.

```js
function Person(name) {
  this.name = name;
}

const me = new Person("John");
console.log(me.__proto__ === Person.prototype); // true
```

### 19.3.3 프로토타입의 constructor 프로퍼티와 생성자 함수

모든 프로토 타입은 constructor 프로퍼티를 지니고 있고, 이 constuctor 프로퍼티는 생성자 함수 참조하고 있어 아래와 같이 사용할 수 있다.

```js
function Person(name) {
  this.name = name;
}

const me = new Person("John");
console.log(me.constructor === Person); // true
```

## 19.4 리터럴 표기법에 의해 생성된 객체의 생성자 함수와 프로토타입

앞서서는 new 연산자와 생성자 함수를 호출하여 인스턴스를 생성하였지만 리터럴 표기법으로 객체를 생성하는 방식도 있다.  
리터럴 표기법에 의해 생성된 객체도 프로토타입이 존재하지만 프로토타입의 constructor 프로퍼티가 가리키는 생성자 함수가 반드시 객체를 만든 생성자 함수라고 할 수 없다.

```js
const obj = {};
const fn = function () {};
const arr = [];
const reg = /^/g;

console.log(obj.constructor === Object); // true
```

위의 예제에서 `console.log(obj.constructor === Object);` 는 true 이다. 그렇다면 리터럴 표기법으로 생성된 객체는 사실상 Object 생성자 함수로 생성되는 것인가?  
[ECMAScript 사양](https://262.ecma-international.org/13.0/#sec-object-value)을 확인해보면 생성자함수에 인수를 전달하지 않거나 undefined, null 일 경우 내부적으로는 추상연산 OrdinaryObjectCreate를 호출하여 Object.prototype 을 프로토타입으로 갖는 빈 객체를 생성한다.

> 💿 &nbsp; 추상연산  
> ECMAScript 사양에서 내부 동작의 구현 알고리즘을 표현한 것으로 설명을 위해 사용되는 함수와 유사한 의사코드라고 이해하자.

```js
// 1. new.target undefined이거나 Object 가 아닌 경우
// 인스턴스 -> Foo.prototype -> Object.prototype 순으로 프로토타입 체인 생성
class Foo extends Object {}
new Foo(); // Foo {}

// 2. Object 생성자 함수에 의한 객체 생성
// 인수가 전달되지 않았을 때 추상 연산 OrdinaryObjectCreate 호출하여 빈 객체 생성
let obj = new Object();
console.log(obj); // {}

// 3. 인수가 전달된 경우는 인수를 객체로 반환
// Number 객체 생성
obj = new Object(1);
console.log(obj); // Number {1}
```

객체 리터럴이 평가될 때는 [링크](https://262.ecma-international.org/13.0/#sec-object-initializer-runtime-semantics-evaluation)와 같은 사양으로 생성된다.  
생성자 함수의 호출과 추상연산 OrdinaryObjectCreate 를 호출하는 것은 똑같으나 세부 내용이 다름을 알 수 있다. 따라서 객체 리터럴에 의해 생성된 객체는 Object 생성자 함수가 생성한 객체가 아니다. 함수 객체 역시 마찬가지이다.

리터럴 표기법에 의해 생성된 객체도 상속을 위해 프로토타입이 필요하다. 따라서 가상적인 생성자 함수를 가지며 **프로토타입과 생성자 함수는 단독으로 존재할 수 없고 언제나 쌍으로 존재한다.**

## 19.5 프로토타입의 생성 시점

프로토타입은 생성자 함수가 생성되는 시점에 더불어 생성된다. 사용자 정의 생성자 함수와 빌트인 생성자 함수를 구분하여 프로토타입 생성 시점을 알아보자.

### 19.5.1 사용자 정의 생성자 함수와 프로토타입 생성 시점

앞에서 알아본 바와 같이 [[constructor]] 내부 메서드를 갖는 함수 객체는 new 연산자와 함께 생성자 함수로서 호출할 수 있다.

프로토타입과 constructor(생성자함수로서 호출할 수 있는 함수) 함수 정의가 평가되어 함수 객체를 생성하는 시점에 같이 생성된다.

```js
console.log(Person.prototype); // {constructor: ƒ}

function Person(name) {
  this.name = name;
}
```

사용자 정의 함수에서 생성된 프로토타입은 constructor 만을 가지고 있는 객체이고 객체는 프로토타입을 가지므로 프로토타입을 지니고 있고 이 프로토타입은 언제나 Object.prototype 이다.

### 19.5.2 빌트인 생성자 함수와 프로토타입 생성 시점

빌트인 생성자 함수가 생성되는 시점에 프로토타입이 생성된다. 빌트인 생성자 함수는 전역 객체가 생성되는 시점에 생성되며 프로토타입도 이 시점에 생성이 된다.  
이후 생성자 함수 또는 리터럴 표기법으로 객체를 생성하면 프로토타입은 생성된 객체의 [[Prototype]] 내부 슬롯에 할당된다.

> 💿 &nbsp; 전역 객체  
> 전역 객체는 코드가 실행됙 이전 단계에 자바스크립트 엔진에 의해 생성되는 특수한 객체

## 19.6 객체 생성 방식과 프로토타입의 결정

객체의 다음과 같은 다양한 생성 방법이 있다.

- 객체 리터럴
- Object 생성자 함수
- 생성자 함수
- Object.create 메서드
- 클래스

다양한 방식으로 생성되는 모든 객체는 세부적인 객체 생성 방식에서는 차이가 있으나 추상 연산 OrdinaryObjectCreate 에 의해 생성된다는 공통점이 있다.  
OrdinaryObjectCreate 는 필수적으로 자신이 생성할 객체의 프로토타입을 인수로 전달 받고 빈 객체를 만든 후 [[Prototype]] 내부 슬롯에 할당한 뒤 해당 객체를 반환한다. 따라서 프로토타입은 추상 연산 OrdinaryObjectCreate 에 전달되는 인수에 의해 결정되며 이 인수는 객체가 생성되는 시점에 객체 생성 방식에 의해 결정된다.

### 19.6.1 객체 리터럴에 의해 생성된 객체의 프로토타입

자바스크립트 엔진은 객체 리터럴을 평가하여 객체를 생성할 때 추상 연산 OrdinaryObjectCreate 를 호출하고 이 때 전달되는 프로토타입은 Object.prototype 이다. 즉, 객체 리터럴에 의해 새엇ㅇ되는 개체의 프로토타입은 Object.prototype 이다.

```js
const obj = { x: 1 };
console.log(obj.constructor === Object); // true
console.log(obj.hasOwnProperty("x")); // true
```

### 19.6.2 Object 생성자 함수에 의해 생성된 객체의 프로토타입

Object 생성자 함수를 인수 없이 호출할 경우 객체리터럴에 의해 생성되는 것과 동일하다. 객체가 생성되는 세부과정은 일부 다르겠지만 프로토타입은 동일하다.

> 인수가 있을 경우는 어떻게 되는가?

```js
const obj = new Object();
obj.x = 1;

console.log(obj.constructor === Object); // true
console.log(obj.hasOwnProperty("x")); // true

const obj2 = new Object("tifid");
console.log(obj2);
console.log(ob2.constructor === Object); // true
```

### 19.6.3 생성자 함수에 의해 생성된 객체의 프로토타입

다른 객체 생성 방식과 마찬가지로 추상 연산 OrdinaryObjectCreate 가 호출되며 전달되는 프로토타입은 생성자 함수의 prototype 프로퍼티에 바인딩되어 있는 객체이다.

```js
function Person(name) {
  this.name = name;
}

const me = new Person("Haloo");
// me.sayHello(); 여기서 실행할 경우 에러 난다.

Person.prototype.sayHello = function () {
  console.log(`Hello, my name is ${this.name}`);
};

const other = new Person("John");

me.sayHello(); // Hello, my name is Haloo
other.sayHello(); // Hello, my name is John
```

## 19.7 프로토타입 체인

```js
function Person(name) {
  this.name = name;
}
Person.prototype.sayHello = function () {
  console.log(`Hello, my name is ${this.name}`);
};
const me = new Person("Haloo");

console.log(me.hasOwnProperty("name")); // true
```

위 예제에서 `hasOwnProperty` 는 Object.prototype 의 메서드이다. me 객체가 이 메서드를 사용한다는 것은 Person.prototype 만이 아닌 Object.prototype 또한 상속받았다는 것을 의미한다.

```js
Object.getPrototypeOf(me) === Person.prototype; // true
Object.getPrototypeOf(Person.prototype) === Object.prototype; // true
```

자바스크립트는 객체의 프로퍼티에 접근하려고 할 때 해당 객체에 접근하려는 프로퍼티가 없다면 [[Prototype]] 내부 슬롯의 참조를 따라 자신의 부모 역할을 하는 프로토타입의 프로퍼티를 순차적으로 검색하고 이를 `프로토타입 체인` 이라 한다. 프로토타입 체인은 자바스크립트가 객체지향 프로그래밍의 상속을 구현하는 메커니즘 이다.

위 예제에서 `me.hasOwnProperty("name")`가 호출되는 과정은 아래와 같다.

1. me 객체에서 hasOwnProperty 메서드를 찾는다. 없으므로 [[Prototype]] 에 바인딩되어 있는 Person.prototype 이동한다.
2. Person.prototype 객체에서 hasOwnProperty 메서드를 찾는다. 없으므로 [[Prototype]] 에 바인딩되어 있는 Object.prototype 이동한다.
3. Object.prototype 객체에서 hasOwnProperty 메서드를 찾는다. 존재하므로 자바스크립트 엔진은 Object.prototype.hasOwnProperty 메서드를 호출하고 메서드의 this 에 me 객체가 바인딩 된다.

> this 에 me 객체가 바인딩 되는 건 뒤에서 자세히 알려준다고 한다.

프로토타입 체인의 최상위에 위치하는 객체는 언제나 Object.prototype 이고 프로토타입 체인의 종점이라고 한다. 종점이므로 Object.prototype의 [[Prototype]] 은 null 이다.

> 🚨 &nbsp;프로토타입 체인으로 종점까지 와서도 프로퍼티를 찾지 못 할 경우 에러가 아닌 undefined 반환하므로 주의하자.

- 프로토타입 체인: 상속과 프로퍼티 검색을 위한 메커니즘
- 스코프 체인: 식별자 검색을 위한 메커니즘

위 두 메커니즘은 서로 연관없이 별도로 동작하는 것이 아니라 서로 협력하여 식별자와 프로퍼티를 검색하는데 사용된다.

## 19.8 오버라이딩과 프로퍼티 새도잉

```js
function Person(name) {
  this.name = name;
}
Person.prototype.sayHello = function () {
  console.log(`Hello, my name is ${this.name}`);
};

const me = new Person("John");
me.sayHello = function () {
  console.log(`Hi, my name is ${this.name}`);
};

me.sayHello(); // "Hi, my name is John"
delete me.sayHello;
me.sayHello(); // "Hello, my name is John"

delete me.sayHello;
me.sayHello(); // "Hello, my name is John"

delete Person.prototype.sayHello;
me.sayHello(); // TypeError: me.sayHello is not a function
```

위 예제에서 알 수 있듯이 프로토타입에 해당하는 메서드가 있지만 인스턴스에서 똑같은 이름으로 사용할 경우 인스턴스에 있는 메서드가 사용된다. 프로토타입 체인은 인스턴스부터 검색해서 올라가기 때문에 당연한 일이며 이렇게 상속관계에 의해 가려지는 현상을 프로퍼티 `새도잉`이라고 하고 상위 클래스가 가지고 있는 메서드를 항위 클래스가 재정의 하는 방식을` 오버라이딩`이라고 한다.

인스턴스에 있는 메서드를 삭제할 경우 프로토타입 체인에 의해 호출되는 것을 확인할 수 있고 프로토타입 프로퍼티를 변경 또는 삭제하려면 하위 객체를 통해 프로토타입 체인으로 접근하는 것이 아니라 프로토타입에 직접 접근해야 한다.

> 오버로딩도 있는데 같은 이름을 가지고 있는 매개변수의 타입과 개수가 다른 메서드를 구현하여 구별하여 호출하는 방식으로 자바스크립트에서는 지원을 하지 않지만 구현은 가능하다.

## 19.9 프로토타입 교체

프로토타입은 생성자 함수 또는 인스턴스에 의해 교체할 수 있다.

### 19.9.1 생성자 함수에 의한 프로토타입 교체

```js
function Person(name) {
  this.name = name;
}
Person.prototype = {
  sayHello() {
    console.log(`Hello, my name is ${this.name}`);
  },
};

const me = new Person("John");
console.log(me.constructor); // Object

Person.prototype.constructor = Person;
console.log(me.constructor); // Person
```

프로토타입을 교체한 객체 리터럴에는 constructor 가 없다. 해당 프로퍼티는 자바스크립트 엔진이 프로토타입을 생성할 때 암묵적으로 추가한 프로퍼티이기 때문이다. 따라서 위 예제에서 첫 번째 me.constructor 는 Person 이 아닌 Object 이다.

### 19.9.2 인스턴스에 의한 프로토타입 교체

프로토타입은 인스턴스의 \_\_proto\_\_ 접근자 프로퍼티로도 접근이 가능하다. 해당 프로퍼티를 통해서 교체할 수 있다.

```js
function Person(name) {
  this.name = name;
}

const me = new Person("John");
console.log(me.constructor); // Person

const protoSample = {
  sayHello() {
    console.log(`Hello, my name is ${this.name}`);
  },
};
Object.setPrototypeOf(me, protoSample);
console.log(me.constructor); // Object
```

해당 방법을 사용할 경우 생성자함수의 prototype 프로퍼티의 참조가 끊기게 된다. 아래의 예제와 같이 복구할 수 있다.

```js
function Person(name) {
  this.name = name;
}

const me = new Person("John");
const protoSample = {
  sayHello() {
    console.log(`Hello, my name is ${this.name}`);
  },
};
Object.setPrototypeOf(me, protoSample);

// 처음 생성될 때 만들어진 프로토타입을 참조하고 있다.
console.log(Person.prototype); // {constructor: ƒ}

// 연결
Person.prototype = protoSample;
console.log(Person.prototype); // {sayHello: ƒ}
```

이처럼 직접 교체하는 것은 꽤나 번거롭기 때문에 더 편리한 방법들을 지원하고 뒤에서 다뤄보자.

## 19.10 instanceof 연산자

이항 연산자로 좌변에 객체를 가리키는 식별자, 우변에 생성자 함수를 가리키는 식별자를 피연산자로 받는다. 피연산자가 함수가 아닐 경우 TypeError 를 발생시킨다.

우변의 생성자 함수의 prototype 에 바인딩된 객체가 좌변 객체의 프로토타입 체인 상에 존재하면 `true`, 아니면 `false` 를 반환한다.

```js
function Person(name) {
  this.name = name;
}

const me = new Person("John");
console.log(me instanceof Person); // true
console.log(me instanceof Object); // true
console.log(me instanceof Array); // false

const protoSample = {
  sayHello() {
    console.log(`Hello, my name is ${this.name}`);
  },
};

// 1
Object.setPrototypeOf(me, protoSample);
console.log(me instanceof Person); // false
console.log(me instanceof Object); // true

// 2
Person.prototype = protoSample;
console.log(me instanceof Person); // true
console.log(me instanceof Object); // true
```

1에서 프로토타입을 객체 리터럴로 직접 교체하여 생성자 함수의 prototype 의 me 의 프로토타입에 대한 참조가 끊어졌다. 따라서 1에서 `console.log(me instanceof Person);`는 false가 나오며 바인딩한 2에서는 true가 나온다.

**instanceof 는 생성자 함수의 prototype 에 바인딩된 객체가 좌변 객체의 프로토타입 체인 상에 존재하는지 확인한다.**

```js
// instanceOf 직접 구현
function isInstanceOf(instance, constructor) {
  const proto = Object.getPrototypeOf(instance);
  if (proto === null) {
    return false;
  }
  return proto === constructor.prototype || isInstanceOf(proto, constructor);
}
function Person(name) {
  this.name = name;
}
const me = new Person("John");

console.log(isInstanceOf(me, Person)); // true
console.log(isInstanceOf(me, Object)); // true
console.log(isInstanceOf(me, Array)); // false
```

## 19.11 직접 상속

### 19.11.1 Object.create에 의한 직접 상속

Object.create 메서드는 명시적으로 프로토타입을 지정하여 새로운 객체를 생선한다. 다른 객체 생성과 마찬가지로 추상 연산 OrdinaryObjectCreate 를 호출한다.

```js
/**
 * 지정된 프로토타입 및 프로퍼티를 갖는 새로운 객체를 생성하여 반환한다.
 * @param {Object} prototype
 * @param {Object} [properiesObject]
 * @return {Object}
 */
Object.create(prototype[, propertiesObject]);
```

```js
// Object.prototype 상속 받지 못함.
const obj = Object.create(null);
console.log(Object.getPrototypeOf(obj) === null); // true

obj.hasOwnProperty("toString"); // TypeError
obj.toString(); // TypeError
```

객체를 생성하면서 직접적으로 상속을 구현하는 것인데 이것의 장점은 다음과 같다.  
~~명시적이기는 하나 견문이 작아 솔직히 장점이라고 잘 와닿지 않는다. 💦~~

- new 연산자가 없이도 객체를 생성할 수 있다.
- 프로토타입을 지정하면서 객체를 생성할 수 있다.
- 객체 리터럴에 의해 생성된 객체도 상속받을 수 있다.

Object.prototype 빌트인 메서드는 직접 호출하는 것은 권장되지 않는데 위 예제처럼 프로토타입 체인의 종점에 위치하는 객체를 만들 경우 에러가 발생하기 때문이다. 따라서 아래와 같은 간접적인 호출이 권장된다.

```js
const obj = Object.create(null);
obj.a = 1;

console.log(Object.prototype.hasOwnProperty.call(obj, "a")); // true
```

### 19.11.2 객체 리터럴 내부에서 \_\_proto\_\_ 에 의한 직접 상속

객체 리터럴 내부에서 \_\_proto\_\_ 를 통해 직접 상속을 구현할 수 있다.

```js
const myProto = {
  x: 2,
};
const obj = {
  __proto__: myProto,
  y: 3,
};

console.log(obj.x, obj.y); // 2, 3
console.log(Object.getPrototypeOf(obj) === myProto); // true
```

## 19.12 정적 프로퍼티/메서드

정적 프로퍼티/메서드는 생성자 함수로 인스턴스를 생성하지 않아도 참조/호출이 가능한 프로퍼티/메서드이다.

```js
function Person(name) {
  this.name = name;
}
Person.staticProp = "static!";
Person.staticMethod = function () {
  return "static method";
};

const me = new Person("John");
Person.staticMethod(); // "static method"
me.staticMethod(); // TypeError
```

위 예제에서 알 수 있듯이 정적 메서드/프로퍼티는 인스턴스에 호출할 수 없다. 프로토타입에 속하지 않는다는 말이다. 앞서 사용하였던 Object.create 와 같다.  
인스턴스/프로토타입 메서드 내에서 this 를 사용하지 않는다면 해당 메서드는 정적 메서드로 변경하여도 동일하게 동작한다.

> 🚨&nbsp; 문서에서 prototype를 #으로 표기하는 경우도 있으니 알아두자  
> Object.prototype.isPrototypeOf => Object#isPrototypeOf

## 19.13 프로퍼티 존재 확인

### 19.13.1 in 연산자

in 연산자는 객체 내에 특정 프로퍼티가 존재하는지 여부를 확인한다.

```js
/**
 * key: 프로퍼티 키를 나타내는 문자열
 * object: 객체로 평가되는 표현식
 */
key in object;
```

```js
const person = {
  name: "John",
  age: 30,
};
console.log("name" in person); // true
console.log("age" in person); // true
console.log("job" in person); // false
```

**in 연산자는 확인 대상 객체의 프로퍼티 뿐만 아니라 상속받은 모든 프로토타입의 프로퍼티를 확인한다.**

in 연산자 대신 ES6에서 도입된 Reflect.has 메서드를 사용할 수 있다.

```js
const person = {
  name: "John",
  age: 30,
};
console.log(Reflect.has(person, "name")); // true
console.log(Reflect.has(person, "age")); // true
console.log(Reflect.has(person, "job")); // false
console.log(Reflect.has(person, "toString")); // true
```

### 19.13.2 Object.prototype.hasOwnProperty 메서드

Object.prototype.hasOwnProperty 메서드를 사용해서도 특정 프로퍼티가 존재하는지 확인 할 수 있다.  
다만 객체 고유의 프로퍼티 키인 경우만 true 를 반환하고 상속받은 프로퍼티는 false 를 반환한다.

```js
const person = {
  name: "John",
  age: 30,
};
console.log(Object.prototype.hasOwnProperty.call(person, "name")); // true
console.log(Object.prototype.hasOwnProperty.call(person, "job")); // false
console.log(Object.prototype.hasOwnProperty.call(person, "toString")); // false
```

## 19.14 프로퍼티 열거

### 19.14.1 for...in 문

객체의 모든 프로퍼티를 순회하며 열거한다.

```js
const protoSample = {
  type: "human",
};
const person = {
  name: "John",
  age: 30,
  __proto__: protoSample,
};

for (const key in person) {
  console.log(`${key}: ${person[key]}`);
}
// name: John
// age: 30
// type: human
```

원래는 in 연산자처럼 상속받은 프로토타입의 프로퍼티까지 열거하지만 toString 과 같은 프로퍼티가 열거되지 않는 것은 toString 프로퍼티는 열거되지 않도록 정의된 프로퍼티이기 때문이다. 앞선 16장에서 배웠다.  
또한 키가 Symbol 프로퍼티는 열거하지 않는다.

```js
console.log(Object.getOwnPropertyDescriptor(Object.prototype, "toString"));
// {writable: true, enumerable: false, configurable: true, value: ƒ}
const sym = Symbol();
const obj = {
  a: 1,
  [sym]: 2,
};

for (const key in obj) {
  console.log(key);
}
// a
```

> 🚨 &nbsp; 순회할 때 프로퍼티의 순서는 보장되지 않는다!!  
> 모던 브라우저에서는 순서를 보장하고 키가 숫자일 경우는 정렬을 하지만 이는 브라우저의 구현이고 사양에서 보장하는 건 아니다.

배열에서는 배열도 객체이기 때문에 프로퍼티와 상속받은 프로퍼티까지 순회할 수 있으므로 for...in 문 보다 for...of 나 Array.prototype.forEach 메서드를 사용을 권장하고 이는 뒤에서 자세히 다루도록 하자.

### 19.14.2 Object.keys/values/entries 메서드

객체 자신의 고유 프로퍼티만 열거하기 위해서는 Object.keys/values/entries 메서드를 사용하는 것을 권장한다.
해당 메서드들은 각각 열거 가능한 프로퍼티 키, 값, 키와 값의 쌍을 배열로 반환한다.

```js
const protoSample = {
  type: "human",
};
const person = {
  name: "John",
  age: 30,
  __proto__: protoSample,
};

console.log(Object.keys(person)); // ["name", "age"]
console.log(Object.values(person)); // ["John", 30]
console.log(Object.entries(person)); // [["name", "John"], ["age", 30]]
```

# 20 strict mode

# 🔥 추가학습 필요

- 추상연산?
- OrdinaryObjectCreate 함수
