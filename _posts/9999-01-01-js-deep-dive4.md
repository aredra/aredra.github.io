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

# 20. strict mode

## 20.1 strict mode?

아래의 예제를 보자 x는 따로 선언되지 않았으므로 ReferenceError가 발생하거나 undefined 가 출력될 것 같지만 10이 출력된다  
자바스크립트 엔진이 암묵적으로 전역 객체에 x 프로퍼티를 동적 생성하기 때문인데 이러한 현상을 암묵적 전역이라고 한다. ~~오류의 원인이 될 가능성이 높으므로 사용하지는 말자~~

이러한 오류 유발 가능성이 높은 경우를 미리 제외하기 위해 엄격한 문법을 적용하는 것을 strict mode 라고 한다. ESLint 와 유사하다.

```js
function foo() {
  x = 10;
}
foo();

console.log(x); // 10
```

## 20.2 strict mode 적용

사용법은 간단하다. 전역의 선두 또는 함수 몸체의 선두에 `'use strict';` 추가하면 된다.

```js
"use strict";

function foo() {
  // 'use strict';
  x = 10; // ReferenceError
}
foo();
```

## 20.3 전역에 strict mode 적용하는 것은 피하자

전역에 적용한 strict mode 는 스크립트 단위로 적용된다.

```html
<!DOCTYPE html>
<html>
  <body>
    <script>
      "use strict";
      x = 10; // ReferenceError
    </script>
    <script>
      (function () {
        "use strict";
        y = 20; // ReferenceError
      })();
      y = 10;
    </script>
  </body>
</html>
```

위 예제와 같이 스크립트 단위로 적용된 strict mode는 다른 스크립트에 영향을 주지 않는다. 하지만 서드파티 라이브러리를 사용하는 경우는 해당 라이브러리가 non-strict mode 인 경우도 있기 때문에 전역에 strict mode 를 적용하는 것을 바람직하지 못하여 예제와 같이 즉시 실행함수로 스크립트를 전체를 감싸서 스코프를 구분하여 적용한다.

## 20.4 함수 단위 strict mode 적용도 피하자

어떠한 함수에는 strict mode를 적용하고 어떤 함수에 적용하지 안혹 하는 것은 바람직하지 않고, 그렇다고 모든 함수에 일일이 적용하는 것은 번거로운 일이다.  
또한 strict mode 가 적용된 함수가 참조할 함수 외부의 컨텍스트에 strict mode 가 적용되지 않는다면 이또한 문제가 될 수 있어서 즉시 실행 함수로 감싸는 것이 아니면 사용을 피하자.

## 20.5 strict mode 가 발생시키는 에러

- 암묵적 전역: ReferrenceError
- 변수, 함수, 매개변수의 삭제: SyntaxError
- 매개변수 이름의 중복: SyntaxError
- with문 사용: SyntaxError

## 20.6 strict mode 적용에 의한 변화

### 20.6.1 일반함수의 this

일반함수로서 호출할 경우 this 에 undefined 가 바인딩 된다.

```js
function foo() {
  "use strict";
  console.log(this);
}

function bar() {
  console.log(this);
}

foo(); // undefined
bar(); // window
new foo(); // foo {}
new bar(); // bar {}
```

### 20.6.2 arguments 객체

매개변수에 전달된 인수를 재할당하여 변경해도 arguments 객체에 반영되지 않는다.

```js
function foo(a) {
  "use strict";
  a = 10;
  console.log(arguments);
}
function bar(a) {
  a = 10;
  console.log(arguments);
}
foo(1, 2, 3);
// Arguments(3) [1, 2, 3, callee: (...), Symbol(Symbol.iterator): ƒ]
bar(1, 2, 3);
// Arguments(3) [10, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
```

# 21. 빌트인 객체

## 21.1 자바스크립트 객체의 분류

자바스크립트 객체는 다음과 같이 크게 3가지로 분류할 수 있다.

- 표준 빌트인 객체: Object, String, Math, Date ...
- 호스트 객체: DOM, fetch ...
- 사용자 정의 객체: 사용자가 직접 정의한 객체

## 21.2 표준 빌트인 객체

자바스크립트는 Object, Function, RegExp, Symbol 등 40여 개의 표준 빌트인 객체를 제공한다.  
이중 Math, Reflect, JSON 을 제외한 객체는 인스턴스를 생성할 수 있는 생성자 함수 객체다.

- 표준 빌트인 객체(생성자 함수 객체 O): 프로토타입 메서드, 정적 메서드 제공
- 표준 빌트인 객체(생성자 함수 객체 X): 정적 메서드 제공

```js
const num = new Number(1.7);
console.log(Object.getPrototypeOf(num) === Number.prototype); // true
console.log(num.toFixed(2)); // 1.70
console.log(Number.isInteger(num)); // false

const math = new Math(); // TypeError
console.log(Math.PI); // 3.141592653589793
console.log(Math.floor(num)); // 1
```

## 21.3 원시값과 래퍼 객체

문자열, 숫자 등 원시값이 존재하는데 문자열, 숫자 객체를 생성하는 표준 빌트인 함수가 있는 이유는 무엇일까?  
아래의 예제를 보면 원시값은 객체가 이니므로 프로퍼티나 메서드를 가질 수 없는데 객체처럼 사용하고 있다. ~~여지껏 이런 생각을 해본 적이 없다 당연하게 써왔었다... 💦~~

```js
const str = new String("hello");

console.log(str.length); // 5
console.log(str.toUpperCase()); // HELLO
```

이유는 원시값인 문자열, 숫자, 불리언 값의 경우 마침표 표기법(대괄호 표기법)으로 접근할 경우 **자바스크립트 엔진이 일시적으로 원시값을 연관된 객체로 변환해 주기 때문이다.**

문자열, 숫자, 불리언 값에 대해 객체처럼 접근하면 생성되는 `임시 객체`를 `래퍼 객체`라고 한다.

마침표 표기법으로 접근할 경우 래퍼 객체인 String 생성자 함수의 인스턴스가 생성되고 원시값인 문자열을 래퍼 객체의 [[StringData]] 내부 슬롯에 할당한다. 또한 String.prototype 메서드를 상속받아 사용할 수 있다.  
이후 객체 처리가 종료되면 래퍼 객체의 처리가 종료되면 [[StringData]] 슬롯에 할당된 원시값으로 원래 상태로 되돌려지고 레퍼 객체는 GC 의 대상이 된다.

```js
// 1. str 은 원시값
const str = new String("hello");

// 2. str 은 암묵적으로 생성된 래퍼 객체
// str 값 'hello'는 내부 객체의 [[StringData]] 슬롯에 할당된다.
// 래퍼 객체에 name 프로퍼티 추가
str.name = "test";

// 3. str 은 다시 원래의 문자열 [[StringData]] 에 할당된 원시값을 가지게 된다.
// 이 때 2에서 생성한 래퍼 객체를 아무도 참조하지 않으므로
// 생성된 래퍼 객체는 GC 의 대상이 된다.

// 4. str 은 새롭게 생성된 래퍼 객체(2에서 생성된 래퍼 객체가 아니다.)
// 새롭게 생성된 래퍼 객체는 name 프로퍼티가 없다.
console.log(str.name); // undefined
```

Number, Boolean 도 내부 슬롯 이름만 다를 뿐 동일하게 동작한다. Symbol 도 래퍼 객체를 생하기는 하지만 일반적인 원시값과 다르므로 뒷 장에서 자세히 설명한다.  
이처럼 래퍼 객체를 통해 원시값을 객체처럼 사용할 수 있어 해당 생성자 함수와 new 연산자를 사용하여 인스턴스를 생성할 필요도 없고 권장하지도 않는다.

> 🚨 `null`, `undefined` 는 래퍼 객체를 생성하지 않는다.

## 21.4 전역 객체

전역객체는 코드가 실행되기 이전 단계에 자바스크립트 엔진에 의해 어떤 객체보다 먼저 생성되는 특수한 객체이며, 어떤 객체에도 속하지 않는 최상위 객체이다.  
실행 환경에 따라 이름이 다 달랐으나 ES11에서는 globalThis 를 사용하여 일반적으로 지칭할 수 있게 되었다.

전역 객체는 표준 빌트인 객체와 호스트 객체를 가지고 있으며 아래와 같은 특징을 지니고 있다.

- 개발자가 의도적으로 생성할 수 없다. 즉 생성자 함수가 제공되지 않는다.
- 전역 객체의 프로퍼티를 참조할 때 window, global 등 객체 식별자를 생략할 수 있다.
- 전역 객체는 표준 빌트인 객체를 프로퍼티로 지니고 있다.
- 자바스크립트 실행 환경에 따라 추가적인 프로퍼티와 메서드를 가진다.
- var 키워드로 선언한 전역 변수와 선언하지 않은 변수에 값을 할당한 암묵적 전역, 그리고 전역함수는 전역 객체의 프로퍼티가 된다.
- let, const 키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 아니다.
- 브라우저 환경의 모든 자바스크립트 코드는 하나의 전역 객체를 공유한다.

### 21.4.1 빌트인 전역 프로퍼티

- Infinity
- NaN
- undefined

### 21.4.2 빌트인 전역 함수

#### eval

자바스크립트 코드를 문자열로 받아 런타임에 평가 및 실행하는 함수이다. 보안에 굉장히 취약하며 실행되는 코드들은 자바스크립트 엔진에 의해 최적화가 수행되지 않으므로 처리 속도가 느리다. 현재는 사용을 금지하고 있다.

> 실행조차 안되는 브라우저도 있을 수 있다.

```js
/**
 * 주어진 문자열 코드를 런타임에 평가 또는 실행
 * @param {string} code 자바스크립트 코드를 나타내는 문자열
 * @return {any} 평가/실행 결과
 */
eval(code);

eval("1 + 1"); // 2
eval("var a = 1;"); // undefined
eval(a); // 1
```

eval 함수는 기존 스코프를 런타임에 동적으로 수정하며, strict mode 인 경우는 자체적인 스코프를 생성한다.

```js
var x = 1;

function foo() {
  var x = 2;
  eval("x = 3;");
  console.log(x); // 3
}
function bar() {
  "use strict";
  var x = 2;
  eval("x = 3;");
  console.log(x); // 2
}

foo();
bar();
```

#### isFinite

인수를 타입변환하여 정상적인 유한수이면 true, 아니면 false를 반환한다.

```js
/**
 * 유한수인지 확인
 * @param {number} n 유한수인지 확인할 숫자
 * @return {boolean} 유한수이면 true, 아니면 false
 */
isFinite(n);

isFinite(null); // true
```

> 🚨 &nbsp; null 은 숫자로 타입변환 할 경우 0 이 된다.

#### isNaN

인수를 타입변환하여 NaN이면 true, 아니면 false를 반환한다.

```js
/**
 * NaN인지 확인
 * @param {number} n NaN인지 확인할 숫자
 * @return {boolean} NaN이면 true, 아니면 false
 */
isNaN(n);
```

#### parseFloat

전달받은 문자열 인수를 부동 소수점 숫자, 실수로 해석하여 반환  
공백으로 구분하며 첫 번째 문자열만 변환하고 앞뒤 공백은 무시된다.

```js
/**
 * 문자열을 실수로 해석
 * @param {string} s 해석할 문자열
 * @return {number} 해석된 실수
 */
parseFloat(s);

parseFloat("99 years"); // 99
parseFloat(" 99 "); // 99
```

#### parseInt

전달받은 문자열 인수를 정수로 해석하여 반환  
공백으로 구분하며 첫 번째 문자열만 변환하고 앞뒤 공백은 무시된다.  
진수로 변환할 수 없는 문자열을 받을 경우 그 뒤는 무시하며 첫번째 문자열 경우 NaN을 반환한다.  
0x 로 시작하는 16진수 문자열만 지수 입력없이 반환이 가능하다.

```js
/**
 * 문자열을 정수로 해석
 * @param {string} s 해석할 문자열
 * @param {number} [radix] 진수
 * @return {number} 해석된 정수
 */
parseInt(s, radix);

parseInt("99 years", 10); // 99
parseInt(" 99 ", 10); // 99
parseInt("99", 16); // 153
parseInt("0x99", 16); // 153
parseInt("Oo13"); // NaN 16진수만 사용가능하다.
parseInt("a19", 10); // NaN

// 숫자는 Number.prototype.toString 메서드를 통해서 해당하는 진수 문자열로 변환이 가능하다.
Number(16).toString(16); // "10"
```

#### encodeURI/decodeURI

완전한 URI를 문자열로 전달받아 이스케이프 처리를 위해 인코딩한다.  
여기서 인코딩이란 URI 문자들을 이스케이프 처리하여 네트워크를 통해 정보를 공유할 때 어떤 시스템에서도 읽을 수 있는 아스크 문자 셋으로 변환하는 것이다.  
숫자, 알파벳, `-_.!~*'()` 문자는 이스케이프 처리에서 제외된다.
decodeURI 의 경우는 이스케이프 처리된 문자열을 원래의 문자열로 변환한다.

```js
/**
 * URI 인코딩
 * @param {string} s 인코딩할 문자열
 * @return {string} 인코딩된 문자열
 */
encodeURI(s);

/**
 * URI 디코딩
 * @param {string} s 디코딩할 문자열
 * @return {string} 디코딩된 문자열
 */
decodeURI(s);
```

#### encodeURIComponent/decodeURIComponent

URI 구성요소를 인수로 전달받아 인코딩하는다. 제외되는 문자열은 encodeURI 와 동일하다.  
인수로 전달된 문자열을 구성요소의 일부분으로 간주하기 때문에 구분자로 사용되는 `=?&` 까지 인코딩한다.

### 21.4.3 암묵적 전역

앞서 암묵적 전역에 대해 간단히 언급을 했었다. 선언되지 않은 변수에 값을 할당할 경우 전역객체에 동적으로 프로퍼티를 생성하는데 이는 프로퍼티 이기 때문에 호이스팅이 발생하지 않는다. 또한 전역 변수와 달리 delete 연산자로 삭제가 가능하다.

```js
console.log(x); // undefined
console.log(y); // ReferenceError

var x = 10;

function foo() {
  // 선언하지 않은 식별자에 값을 할당
  // 암묵적 전역 발생
  y = 20;
  console.log(x + y); // 30
}

foo();

console.log(window.x); // 10
console.log(window.y); // 20

delete x; // 삭제 안됨
delete y; // 프로퍼티는 삭제됨

console.log(window.x); // 10
console.log(window.y); // undefined
```

# 22. this

## this 키워드

개인적으로는 설명이 좀 어렵다고 생각하는데 좌우지간 자바스크립에서 객체지향 프로그래밍으로 생각한다면 객체는 상태를 나타내느 프로퍼티와 동작을 나타내는 메서드를 가지는 자료구조이다.  
여기서 메서드는 자신이 속한 객체의 상태를 참조 및 변경을 할 수가 있어야 하는데 생성되는 객체의 식별자를 일반적으로 알 수가 없다.  
재귀적으로 참조하는 방법이 있을 수 있으나 바랍직하지 않다. 그래서 제공하는 식별자가 this 이다.

**this 는 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 자기 참조 변수다. this를 통해 자신이 속한 객체 또는 자신이 생성할 인스턴스의 프로퍼티나 메서드를 참조할 수 있다.**

this 는 자바스크립트 엔진에 의해 암묵적으로 생성되며, 코드 어디서든 참조가 가능하다. 함수를 호출하면 arguments 객체와 this 가 암묵적으로 함수 내부에 전달되고 이 둘을 지역 변수처럼 사용할 수 있다.  
this 가리키는 값, 즉 this 바인딩은 함수 `호출 방식에 의해 동적으로 결정`된다.

> 🚨&nbsp; 클래스 기반 언어에서는 대부분 this 는 클래스가 생성하는 인스턴스를 가리키지만 자바스크립트는 함수가 호출되는 방식과 strict modedp 따라 this 에 바인딩 되는 값이 동적으로 결정됨을 유의하자.

```js
// this 는 어디서든 참조 가능
// 전역에서는 전역 객체
console.log(this); // Window {...}

function square(x) {
  // 일반 함수 내부에서는 this 는 전역
  console.log(this); // Window {...}
  return x * x;
}
square(2); // 4

let Person = {
  name: "사람",
  sayName() {
    // 메서드 내부에서 this 는 호출한 객체
    console.log(this); // {name: '사람', sayName: ƒ}
    return this.name;
  },
};
console.log(Person.sayName()); // 사람

function Person2(name) {
  this.name = name;
  // 생성자 함수 내부에서 this 는 생성하는 인스턴스
  console.log(this); // Person2 {name: '자바스크립트'}
}

const me = new Person2("자바스크립트");
```

this 는 객체의 프로퍼티나 메서드를 참조하기 위한 자기 참조 변수이므로 일반적으로 객체의 메서드 내부 또는 함수 내부에서만 의미가 있다. 따라서 일반 함수 내부에서는 this 를 사용할 필요가 없기 때문에 앞서 적었던 것 처럼 stirct mode 에서는 this 에 undefined 가 바인딩 된다.

## 22.2 함수 호출 방식과 this 바인딩

this 바인딩은 함수 호출 방식에 따라 동적으로 결정된다. 호출 방식은 다음과 같다.

- 일반 함수 호출
- 생성자 함수 호출
- 메서드 호출
- Function.prototype.call/apply/bind 메서드에 의한 간접 호출

> 🚨 &nbsp; 렉시컬 스코프와 this 바인딩은 결정 시기가 다르다.  
> 함수의 상위 스코프를 결정하는 방식인 렉시컬 스코프는 함수 정의가 평가되어 함수 객체가 생되는 시점에 상위 스코프를 결정하고, this 바인딩은 함수가 호출되는 시점에 결정된다.

### 22.2.1 일반 함수 호출

기본적으로 this 에는 전역 객체가 바인딩 된다. strict mode 일 경우 this 는 undefined 로 바인딩 된다.  
화살표 함수의 this 는 상위 스코프의 this 를 가리킨다. 아래 예제에서 화살표 함수를 쓰지 않을 경우 전역 객체를 참조하여 2 가 출력될 것이다.

```js
var value = 2;

const obj = {
  value: 100,
  foo() {
    setTimeout(() => {
      console.log(this.value);
    }, 1000);
  },
};

obj.foo(); // 100
```

### 22.2.2 메서드 호출

메서드를 호출한 객체, 메서드 이름 앞의 마침표 연산자 앞에 기술한 객체가 바인딩된다.  
메서드는 생성한 객체에 포함되는 것이 아니라 독립적으로 존재하는 별도의 객체이고, 따라서 다른 객체의 프로퍼티에 할당하는 것으로 다른 객체의 메서드, 일발 변수에 할당할 수 있다.

```js
const person = {
  name: "Lee",
  sayName() {
    console.log(this.name);
  },
};

const anoterPerson = {
  name: "Jane",
  hello() {
    console.log(`Hello, I'm ${this.name}`);
  },
};

anoterPerson.sayName = person.sayName;
anoterPerson.sayName(); // Jane

const getName = person.sayName;
getName(); // ''
```

### 22.2.3 생성자 함수 호출

생성자 함수 내부의 this 에는 생성자 함수가 생성할 인스턴스가 바인딩 된다.

```js
function Person(name) {
  this.name = name;
  this.sayName = function () {
    console.log(this.name);
  };
}

const lee = new Person("Lee");
lee.sayName(); // Lee
const anotherLee = new Person("Another Lee");
anotherLee.sayName(); // Another Lee
```

### 22.2.4 Function.prototype.call/apply/bind 메서드에 의한 간접 호출

apply, call, bind 메서드는 Function.prototype 의 메서드이다. 모든 함수가 상속받아 사용 할 수 있다.

먼저 apply, call 메서드는 this 로 사용할 객체와 인수 리스트를 인수로 전달 받아 함수를 호출한다. 두 메서드는

```js
/**
 * 주어진 this 바인딩과 인수 리스트 배열을 사용하여 함수를 호출한다.
 * @param {Object} thisArg - this 로 사용할 객체
 * @param {Array} [argArray] - 인수 리스트 배열
 * @returns {Any} 함수의 반환 값
 */
Function.prototype.apply(thisArg[, argArray])

/**
 * 주어진 this 바인딩과 , 로 구분된 인수 리스트를 사용하여 함수를 호출한다.
 * @param {Object} thisArg - this 로 사용할 객체
 * @param {Any} arg1, arg2, ... - 인수 리스트 배열
 * @returns {Any} 함수의 반환 값
 */
Function.prototype.call(thisArg[, arg1[, arg2, ...]])
```

apply, call 메서드의 본질적인 기능은 함수를 호출하는 것이다.

```js
function getThisBinding() {
  console.log(arguments);
  return this;
}

const thisArg = { a: 1 };

console.log(getThisBinding.apply(thisArg, [1, 2, 3])); // { a: 1 }
// Arguments(3) [1, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
console.log(getThisBinding.call(thisArg, 1, 2, 3)); // { a: 1 }
// Arguments(3) [1, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
```

bind 메서드는 함수를 호출하지 않고 this 로 사용할 객체를 전달한다. 메서드의 this 와 메서드 내부의 중첩 함수 또는 콜백 함수의 this 가 불일치하는 문제를 해결할 때 사용하면 유용하다.

```js
const person = {
  name: "Lee",
  foo(callback) {
    console.dir(callback);
    setTimeout(callback.bind(this), 100);
  },
  sayName() {
    console.log(this.name);
  },
};

// 콜백을 에로우로 할 경우 프로토타입이 없어서 제대로 동작 안함
// person.foo(() => console.log(`test: ${this.name}`));
person.foo(function () {
  console.log(`test: ${this.name}`);
});
```

# 🔥 추가학습 필요

- 추상연산?
- OrdinaryObjectCreate 함수
- with 문
