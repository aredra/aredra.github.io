---
title: "JS - private, protected"
date: 2021-08-19
comments: true
read_time: false
toc: true
categories:
  - JS
tags:
  - private
  - protected
---

자바를 사용해보신 분들이라면 `접근제어자(Access Modifier)`를 기억하실 것이다. 
이런 기능이 자바스크립트에서도 슬슬 도입되려는 것 같다.  

## protected

보통 다른 언어에서 `protected` 는 클래스 자신과 자손 클래스에서만 접근을 하용하는 제어자이다. 현재 자바스크립트 스펙에서 지원하는 부분은 아니지만 모방해서 사용하는 경우가 많다. 선언을 할 때 보통 외부접근이 불가능한 프로퍼티나 메서드는 `_` 를 접두사로 사용한다.

> **코드 출처**: [모던 자바스크립트](https://ko.javascript.info/private-protected-properties-methods)

```js
class CoffeeMachine {
  _waterAmount = 0;

  set waterAmount(value) {
    if (value < 0) throw new Error("물의 양은 음수가 될 수 없습니다.");
    this._waterAmount = value;
  }

  get waterAmount() {
    return this._waterAmount;
  }

  constructor(power) {
    this._power = power;
  }

}

// 커피 머신 생성
let coffeeMachine = new CoffeeMachine(100);

// 물 추가
coffeeMachine.waterAmount = -10;

// Error: 물의 양은 음수가 될 수 없습니다.
```

사실 여기서 본래 의도를 벗어난 부분이지만 `coffeeMachine._waterAmount` 로 접근한다면 값 변경이 가능은 하다. setter를 설정하지 않았다고 하더라도 프로퍼티의 `writable` 설정이 `true` 라면 수정이 가능할 것이다.

## private

클래스의 내부에서만 접근이 가능한 제어자이다. 자바스크립트에서 지원을 하고 있으며 `ES2019`부터 사용할 수 있게되었다. 접두사로 `#` 를 붙여서 필드를 선언할 수 있다.
~~IE에서는 지원하지 않는다!!~~

> **코드 출처**: [모던 자바스크립트](https://ko.javascript.info/private-protected-properties-methods)

```js
class CoffeeMachine {

  #waterAmount = 0;

  get waterAmount() {
    return this.#waterAmount;
  }

  set waterAmount(value) {
    if (value < 0) throw new Error("물의 양은 음수가 될 수 없습니다.");
    this.#waterAmount = value;
  }
}

let machine = new CoffeeMachine();

machine.waterAmount = 100;
alert(machine.#waterAmount); 
// Error, JS 스펙상 틀린 Syntax Error
```

**또한 private 필드는 `this[variableName]` 으로 접근이 불가하다.**