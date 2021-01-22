---
title: "CSS animation 간단 정리"
date: 2021-01-20
comments: true
read_time: false
categories:
  - CSS
tags:
  - animation
---

CSS의 animation 속성에 대해 간단히 정리해보자

## animation 속성

얼핏 사용하다보면 `transition`속성과 비슷하다고 생각할 수 있지만 (비슷한 건 사실인 거 같다. 🙄) `animation`의 경우 name을 등록하고 각 구간에 대해 설정이 가능하고, 반복이나 시점 등 다양한 추가 설정이 가능하다.

```css
.animation-box {
  margin: 50px;
  width: 200px;
  height: 200px;
  border-radius: 15px;
  display: flex;
  justify-content: center;
  align-items: center;
  animation: animation 1s linear infinite both alternate;
}
.animation-box::before {
  content: "playing";
  color: white;
  font-size: 1.5rem;
  font-weight: bold;
}
.animation-box:hover {
  animation-play-state: paused;
}
.animation-box:hover::before {
  content: "paused";
  color: black;
  font-size: 1.5rem;
  font-weight: bold;
}
@keyframes animation {
  0% {
    width: 200px;
    background: lightgreen;
  }
  100% {
    width: 500px;
    background: lightcoral;
  }
}
```

## 개별속성

`animation`은 저 단어 하나만 있으면 모든 걸 할 수 있는 단축속성이지만 그래도 개별적인 속성은 아래와 같다.

|            값             |              의미              | 기본값  |
| :-----------------------: | :----------------------------: | :-----: |
|      animation-name       |    `@keyframes`규칙의 이름     |  none   |
|    animation-duration     |         지속시간 설정          |   0s    |
| animation-timing-function | 타이밍 함수(cubic-bezier) 지정 |  ease   |
|      animation-delay      |         대기시간 설정          |   0s    |
| animation-iteraton-count  |         반복횟수 설정          |    1    |
|    animation-direction    |         반복 방향 설정         | normal  |
|    animation-fill-mode    |         전후 상태 설정         |  none   |
|   animation-play-state    |        재생 / 중지 설정        | running |
