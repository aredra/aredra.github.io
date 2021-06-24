---
title: "React - Lifecycle"
date: 2021-06-24
comments: true
read_time: false
toc: true
categories:
  - React
tags:
  - lifecycle
---

`React` 의 `LifeCycle` 에 대해 알아보자.
Vue 와 비슷하게 이미 등록되어 있는 훅을 통해 컴포넌트의 생성과 제거 사이 시점에 원하는 동작을 추가할 수 있다.

v 16.3 기준으로 변동이 있다. 그리고 16.4 에서는 적용 범위 일부가 변동이 있었다. 아래 링크를 참조하시길 바란다.

[리액트 라이프사이클 도표](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)

## v 16.3 미만

16.3 버전 밑으로는 크게 아래와 같은 단계로 나눌 수 있으며, 각 단계에 우측과 같은 메서드들이 있다.

 - `Initialization`: constructor 
 - `Mounting`: componentWillMount, render, componentDidMount
 - `Updation`: componentReceiveProps, shouldComponentUpdate, componentWillUpdate, render, componentDidUpdate
 - `Unmounting`: componentWillUnmount

```js
class Component extends React.Component {
  constructor(props) {
    super(props);

    console.log('constructor');
  }
  
  interval = null;

  state = {
    age: 9999
  }

  render() {
    console.log('render');

    return (
      <div>
        <h2>{this.props.name} -  {this.state.name}, Hello, World!!</h2>
      </div>
    );
  }

  componentWillMount() {
    console.log('componentWillMount');
  }

  componentDidMount() {
    console.log('componentDidMount');
    this.interval = setInterval(this.setState((state) => ({
      ...state,
      age: state.age + 1
    })), 3000);
  }

  componentWillReceiveProps(nextProps) {
    console.log('componentWillReceiveProps', nextProps);
  }

  shouldComponentUpdate(nextProps, nextState) {
    console.log('shouldComponentUpdate', nextProps, nextState);
    // true: Rerender, false: X
    return true;
  }

  componentWillUpdate(nextProps, nextState) {
    console.log('componentWillUpdate', nextProps, nextState);
  }

  componentDidUpdate(prevProps, prevState) {
    console.log('componentDidUpdate', prevProps, prevState);
  }

  componentWillUnmount() {
    clearInterval(this.interval);
  }
}

ReactDOM.render(<Component />, document.querySelector('body'));
```
## v16.3 이후

`componentWillMount, componentReceiveProps` 가 `getDerivedStateFromProps` 로 `componentWillUpdate` 가 사라지고 좀 다른 시점에 `getSnapshowBeforeUpdate` 가 생겼다.  
`getDerivedStateFromProps` 는 **static** 으로 작성해야한다.

```js
class Component extends React.Component {
  constructor(props) {
    super(props);

    console.log('constructor');
  }
  
  interval = null;

  state = {
    age: 9999,
    hasError: false
  }

  render() {
    console.log('render');
    if (this.state.hasError) {
      return <div>에러 발생</div>;
    }

    return (
      <div>
        <h2>{this.props.name} -  {this.state.name}, Hello, World!!</h2>
      </div>
    );
  }

  componentDidCatch(error, info) {
    this.setState({ ...this.state, hasError: true });
  }

  // return 필수, state 를 반환
  static getDerivedStateFromProps(nextProps, prevState) {
    console.log('getDerivedStateFromProps', nextProps, prevState);
    return {};
  }

  componentDidMount() {
    console.log('componentDidMount');
    this.interval = setInterval(this.setState((state) => ({
      ...state,
      age: state.age + 1
    })), 3000);
  }

  componentWillReceiveProps(nextProps) {
    console.log('componentWillReceiveProps', nextProps);
  }

  shouldComponentUpdate(nextProps, nextState) {
    console.log('shouldComponentUpdate', nextProps, nextState);
    // true: Rerender, false: X
    return true;
  }

  componentWillUpdate(nextProps, nextState) {
    console.log('componentWillUpdate', nextProps, nextState);
  }

  getSnapshotBeforeUpdate(prevProps, prevState) {
    // snapshot 을 리턴
  }

  componentDidUpdate(prevProps, prevState, snapshot) {
    console.log('componentDidUpdate', prevProps, prevState);
  }

  componentWillUnmount() {
    clearInterval(this.interval);
  }
}

ReactDOM.render(<Component />, document.querySelector('body'));
```

## 그 외

`v 16.4` 이후 `getDerivedStateFromProps` 의 적용 범위가 넓어졌다. 상단의 참조 링크에 자세히 잘 나와있다. 그리고 React 는 공식 문서가 잘 되어 있는 것 같다.

에러가 발생할 경우 실행되는 메서드도 있다.
`componentDidCatch` - [Error Boundaries](https://ko.reactjs.org/docs/error-boundaries.html)