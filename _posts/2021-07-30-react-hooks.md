---
title: "React - 기초 hooks"
date: 2021-07-30
comments: true
read_time: false
toc: true
categories:
  - React
tags:
  - hooks
---

`react` 의 기본 `hooks` 에 대한 메모

해당 포스트는 [React - 공식문서](https://ko.reactjs.org/docs/hooks-reference.html)의 일부를 작성시점 기준 useEffect 코드 빼고 그대로 복사한 글이다. 자세한 내용은 공식문서에서 추가적으로 확인할 수 있다.

> 아직 나도 다 안 읽어봤지만 공식문서는 한번쯤 읽어보는걸 추천드린다.

## useState

react 라이브러리의 훅으로 상태 유지 값과 그 값을 갱신하는 함수를 반환한다.

```js
const [state, setState] = useState(initialState);

setState(newState);
```

최초로 렌더링을 하는 동안, 반환된 `state`는 `첫 번째 전달된 인자(initialState)`의 값과 같습니다.

`setState` 함수는 `state`를 갱신할 때 사용합니다. 새 `state` 값을 받아 컴포넌트 리렌더링을 큐에 등록합니다.

다음 리렌더링 시에 `useState`를 통해 반환받은 첫 번째 값은 항상 갱신된 최신 `state`가 됩니다.

```js
function Counter({ initialCount }) {
  const [count, setCount] = useState(initialCount);
  return (
    <>
      Count: {count}
      <button onClick={() => setCount(initialCount)}>Reset</button>
      <button onClick={() => setCount((prevCount) => prevCount - 1)}>-</button>
      <button onClick={() => setCount((prevCount) => prevCount + 1)}>+</button>
    </>
  );
}
```

## useEffect

명령형 또는 어떤 effect를 발생하는 함수를 인자로 받습니다.

변형, 구독, 타이머, 로깅 또는 다른 부작용(side effects)은 (React의 렌더링 단계에 따르면) 함수 컴포넌트의 본문 안에서는 허용되지 않습니다. 만약 이를 수행한다면 그것은 매우 혼란스러운 버그 및 UI의 불일치를 야기하게 될 것입니다.

대신에 `useEffect`를 사용하세요. `useEffect`에 전달된 함수는 화면에 렌더링이 완료된 후에 수행되게 될 것입니다. `React`의 순수한 함수적인 세계에서 명령적인 세계로의 탈출구로 생각하세요.

기본적으로 동작은 모든 렌더링이 완료된 후에 수행됩니다만, 어떤 값이 변경되었을 때만 실행되게 할 수도 있습니다.

```js
const [count, setCount] = useState(0);

// useEffect(function, dependency);
useEffect(() => {
  console.log("componentDidMount");

  return () => {
    // cleanup
    console.log("componentWillUnMount");
  };
}, []);

useEffect(() => {
  console.log("componentDidMount && componentDidUpdate by count", count);

  return () => {
    console.log("cleanup by count", count);
  };
}, [count]);

return (
  <div>
    <p>You clicked {count} times in Function component</p>
    <button onClick={click}>Click me</button>
  </div>
);

function click() {
  setCount(count + 1);
}
```

## useContext

`context` 객체(React.createContext에서 반환된 값)을 받아 그 context의 현재 값을 반환합니다. context의 현재 값은 트리 안에서 이 `Hook`을 호출하는 컴포넌트에 가장 가까이에 있는 `<MyContext.Provider>`의 `value prop`에 의해 결정됩니다.

컴포넌트에서 가장 가까운 `<MyContext.Provider>`가 갱신되면 이 `Hook`은 그 `MyContext provider`에게 전달된 가장 최신의 `context value`를 사용하여 렌더러를 트리거 합니다. 상위 컴포넌트에서 `React.memo` 또는 `shouldComponentUpdate를` 사용하더라도 `useContext`를 사용하고 있는 컴포넌트 자체에서부터 다시 렌더링됩니다.

`useContext`로 전달한 인자는 **context 객체 그 자체**이어야 함을 잊지 마세요.

`useContext`를 호출한 컴포넌트는 `context` 값이 변경되면 항상 리렌더링 될 것입니다. 만약 컴포넌트를 리렌더링 하는 것에 비용이 많이 든다면, 메모이제이션을 사용하여 최적화할 수 있습니다.
