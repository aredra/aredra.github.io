---
title: "React - 추가적 hooks"
date: 2021-08-07
comments: true
read_time: false
toc: true
categories:
  - React
tags:
  - hooks
---

[리액트 공식문서](https://ko.reactjs.org/docs/hooks-reference.html)에서 제공하는 기본 `hooks` 외 추가적인 `hooks` 에 대한 메모

## useReducer

`redux` 의 사용방법과 유사(?)하다. ~~useReducer의 구동원리를 직접 확인해보지 않았다.~~ `useState` 를 대체할 수 있으며, 복잡한 로직을 만들거나, `nextState` 값이 `prevState` 값에 의존적일 경우 권장하며, 콜백함수가 아닌 `dispatch`를 넘겨주며 `state`가 필요하지 않은 경우 컴포넌트는 다시 랜더링 되지 않으며 성능적인 이점을 챙길 수 있다.

```jsx
const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };
    case "decrement":
      return { count: state.count - 1 };
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({ type: "decrement" })}>-</button>
      <button onClick={() => dispatch({ type: "increment" })}>+</button>
    </>
  );
}
```

3번째 인자의 경우 함수로 2번째를 인자를 받아 초기 `state` 를 **지연**하여 생성할 수 있다.

```js
function init(initialCount) {
  return { count: initialCount };
}

function reducer(state, action) {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };
    case "decrement":
      return { count: state.count - 1 };
    case "reset":
      return init(action.payload);
    default:
      throw new Error();
  }
}

function Counter({ initialCount }) {
  const [state, dispatch] = useReducer(reducer, initialCount, init);
  return <></>;
}
```

## useCallback

메모이제이션된 **콜백**을 반환한다. 의존성이 변경되었을 경우에만 변경되며, 그냥 선언된 함수로 인해 불필요한 랜더링이 일어나는 것을 방지할 수 있다.

```js
function App() {
  const click = React.useCallback(() => {
    doSomething(a, b);
  }, [a, b]);

  return <div onClick={click}></div>;
}
```

## useMemo

메모이제이션된 **값** 을 반환한다. 의존성을 설정할 수 있고, 해당하는 의존성이 변경되었을 때만 다시 계산한다. 의존성이 없을 경우 랜더링 될 때마다 다시 계산을 하게 된다.  
또한 랜더링 중에 `useMemo` 가 전달받은 함수가 실행된다는 것을 주의해야 한다.

```js
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

> 추가적으로 현재 문서상으로는 useMemo의 방향성이 달라질 수 있으니 사용하지 않는 것을 권장하는 것으로 보인다.

## useRef

컴포넌트 라이프사이클 동안 유지되는 ref 객체를 반환한다. createRef()의 매번 새로 생성되는 부분과 대비된다.

```js
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` points to the mounted text input element
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```

## useImperativeHandle

`ref`로 부모 인스턴스가 불렸을 때 부모 인스턴스에게 전달되는 값을 커스터마이징 할 수 있다.

```js
function FancyInput(props, ref) {
  const inputRef = useRef();
  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    }
  }));
  return <input ref={inputRef} ... />;
}
FancyInput = forwardRef(FancyInput);
```

## useLayoutEffect

기능은 `useEffect`와 동일하지만 **모든 DOM 변경 후** 동기적으로 발생하는 부분이 다르다.

## useDebugValue

React 개발자도구에서 사용자 Hook 레이블을 표시하는 데에 사용할 수 있습니다.
