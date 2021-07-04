---
title: "React - routing"
date: 2021-07-05
comments: true
read_time: false
toc: true
categories:
  - React
tags:
  - routing
---

React routing 에 대한 메모

## Routing

기본적으로 `SPA` 프레임워크의 경우 화면이 이동하지만 해당 페이지의 이동은 서버로부터 요청 받은 새로운 페이지가 아니다. 클라이언트단에서 컴포넌트를 변경하며 화면 변화가 일어나고 이에 새로운 페이지가 나타난것 처럼 보이는 것 뿐이다.

이러한 현상을 Routing 이라고 말할 수 있고, React 에서는 많은 라이브러리가 있지만 많이 사용되는 있는 라이브러리는 아래와 같다.

> `react-router-dom`: facebook 공싱 라이브러리는 아님 하지만 대표적인 라이브러리

## 사용 예

간단하게 사용하는 방법은 아래와 같다. `BrowserRouter` 로 변경이 일어날 부분들을 감싸고 `Route` 를 사용하여 만들면 된다. 이는 실로 간단하게 작성한건고 깊은 부분은 찾아보자.

`exact` 는 완전 경로가 일치할 때만 보여준다.

```js
import { BrowserRouter, Route } from "react-router-dom";
import About from "./pages/About";
import Home from "./pages/Home";
import Profile from "./pages/Profile";

function App() {
  return (
    <BrowserRouter>
      <Route path="/" exact component={Home} />
      <Route path="/profile" exact component={Profile} />
      <Route path="/profile/:id" component={Profile} />
      <Route path="/about" component={About} />
    </BrowserRouter>
  );
}

export default App;
```

## queryString, pathVariable 사용

`pathVariable` 의 경우는 `props.match.param` 에 `queryString` 은 `props.location.search` 에 있으나 꺼내쓰기 번거로운 형태이므로 `URLSearchParams` 을 쓰거나, `query-string` 라이브러리 사용을 **권장**한다.

```js
import queryString from "query-string";

export default function About(props) {
  const searchParams = props.location.search;
  const id = props.match.params.id;

  // const params = new URLSearchParams(searchParams);
  const query = queryString.parse(searchParams);
  console.log(query);
  return (
    <div>
      <h2>About Page</h2>
      {query.name && <p>{query.name}</p>}
    </div>
  );
}
```