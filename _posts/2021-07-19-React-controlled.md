---
title: "React - Controlled/Uncontrolled Component"
date: 2021-07-19
comments: true
read_time: false
toc: true
categories:
  - React
tags:
  - Component
---

`FE` 영역에서 빼놓을 수 없는 부분은 `상태관리`인 것 같다. 그러한 부분에 있어 React 를 학습하던 중 알게된 컴포넌트 개념을 메모한다.

## Controlled? Uncontrolled?

간단하게 HTML Elements 중에는 상태를 가진 요소들 있다.

> input, select, textarea ...

이런 요소들의 상태를 누가 관리하느냐에 따라 `Controlled`, `Uncontrolled` 로 나뉜다.
간단한 예제는 아래의 코드와 같고, 대부분은 Controlled Component 를 사용해야 한다.

**예제가 클래스형 컴포넌트가 불편해도 개인메모용이기에 참아주시길 부탁드린다.**

- `Controlled` : 요소를 가지고 있는 컴포넌트가 관리

```jsx
import React from "react";

class ControlledComponent extends React.Component {
  state = {
    value: "",
  };

  render() {
    return (
      <div>
        <input value={this.value} onChange={this.change} />
      </div>
    );
  }

  change = (e) => {
    console.log(e.target.valule);
    this.setState({ value: e.target.value });
  };
}

export default ControlledComponent;
```

- `Uncontrolled` : 요소의 상태를 관리하지는 않고, 참조만 컴포넌트가 소유

```jsx
import React from "react";

class UncontrolledComponent extends React.Component {
  // 첫 랜더 후 매핑
  inputRef = React.createRef();
  render() {
    console.log("initial", this.inputRef);
    return (
      <div>
        <input ref={this.inputRef} type="text" />
        <button onClick={this.click}>Submit</button>
      </div>
    );
  }

  componentDidMount() {
    console.log("componentDidMount", this.inputRef);
  }

  click = () => {
    console.log(this.inputRef.current.value);
  };
}

export default UncontrolledComponent;
```

## 참조문서

- [React 공식문서 - Controlled Component](https://ko.reactjs.org/docs/forms.html#controlled-components)
- [React 공싱문서 - 제어 컴포넌트 vs. 비제어 컴포넌트](https://ko.reactjs.org/docs/glossary.html#%EC%A0%9C%EC%96%B4-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8-vs-%EB%B9%84%EC%A0%9C%EC%96%B4-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8)
