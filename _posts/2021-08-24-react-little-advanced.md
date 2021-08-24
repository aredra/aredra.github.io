---
title: "React - createPortal, forwardRef"
date: 2021-08-24
comments: true
read_time: false
toc: true
categories:
  - React
tags:
  - createPortal
  - forwardRef
---

React의 `createPortal`, `forwardRef` API 에 대한 메모  
개인적인 메모인지라 자세한 내용은 아래의 공식문서를 보는 것이 이롭다.

## React 공식문서
- [Portals - createPortal](https://reactjs.org/docs/portals.html) 
- [Forwarding Refs - forwarRef](https://reactjs.org/docs/forwarding-refs.html) 

## createPortal

일반적으로 컴포넌트의 요소는 가장 가까운 부모 노드의 자식으로 DOM에 마운트 되지만 `DOM의 특정 다른 위치`에 넣고 싶을 때 유용한 API  
일반적으로 모달, 다이얼로그, 툴팁 등에 유용하게 사용할 수 있다.

#### 이벤트 버블링

`Portal` 을 사용하여 마운트된 컴포넌트에서 발생하는 이벤트 버블링은 일반적인 React 트리의 부모노드가 캐치할 수 있으니 걱정할 필요 없다.

```js
// Modal.jsx
import ReactDOM from "react-dom";

const Modal = ({ children }) =>
  ReactDOM.createPortal(children, document.querySelector("#modal"));

export default Modal;


// App.js
import Modal from "./components/Modal";

function App() {
  const [visible, setVisible] = useState(false);

  const open = () => {
    setVisible(true);
  };

  const close = () => {
    setVisible(false);
  };

  return (
    <div>
      <button onClick={open}>open</button>
      {visible && (
        <Modal>
          <div
            style={{
              background: "rgba(0,0,0,.5)",
            }}
            onClick={close}
          >
            Happy Hacking
          </div>
        </Modal>
      )}
    </div>
  );
}

export default App;


// index.html
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>React App</title>
  </head>
  <body>
    <div id="root"></div>
    <div id="modal"></div>
  </body>
</html>
```

## forwardRef

`ref` 를 전달받아 하부 트리 내의 다른 컴포넌트로 전달 할 수 있는 React 컴포넌트를 생성한다.

- `DOM Element`로 ref 전달
- `HOC` 로 ref 전달 (아직 이 경우는 잘 모르겠다.. 💦)

위 2가지 경우 유용하게 사용할 수 있다.

```js
// MyInput.jsx
import React from "react";

export default React.forwardRef(function MyInput(props, ref) {
  return (
    <div>
      <p>MyInput</p>
      <input ref={ref}></input>
    </div>
  );
});

// App.js
function App() {
  const myInputRef = useRef();

  const click = () => {
    console.log(myInputRef.current.value);
  };

  return (
    <div>
      <MyInput ref={myInputRef}></MyInput>
      <button onClick={click}>send</button>
    </div>
  );
}

export default App;
```

1. `useRef`를 호출해서 React ref를 생성하고 ref 변수에 할당합니다.
2. ref를 JSX 속성으로 지정해서 `<MyInput ref={ref}>`로 전달합니다.
3. React는 이 ref를 `forwardRef` 내부의 (props, ref) => ... 함수의 두 번째 인자로 전달합니다.
4. 이 ref를 JSX 속성으로 지정해서 `<input ref={ref}>`으로 전달합니다.
5. ref가 첨부되면 `ref.current`는 `<input> DOM 노드`를 가리키게 됩니다.