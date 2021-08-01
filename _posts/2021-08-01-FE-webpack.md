---
title: "Webpack - 기초"
date: 2021-08-01
comments: true
read_time: false
toc: true
categories:
  - Web
tags:
  - webpack
---

모던프레임워크(?) Vue, React로 작성된 파일을 바로 브라우저에 줄 때 브라우저는 제대로 인식하지 못하기에(JSX나 ESNext 등..) build를 한 결과물을 브라우저에게 전달하고 이 동작에서 많이 사용되고 있는 번들러가 Webpack 이다.

![navigation-api](/assets/images/2020-build-tools-ranking.png)

모던프레임워크에서만이 아니라 목적에 의해 사용할 수 있다.

## 대표 기능

- 코드 분리: 코드를 여러 덩어리로 나눠 필요할 때 로딩할 수 있다.(Dynamic import ...) 
- 코드 축소: 공백, 변수이름 변환 등 파일 크기를 축소한다.
- 특징 설정하기: Development, Production 등 환경설정 값 등을 설정하여 테스트 등에 적합한 환경으로 구동할 수 있다.
- HMR: 소스의 변경을 감지하여 바뀐 부분을 바꿔준다.(On/Off 안해도 됨)

## 장점

- 모듈성
- 조합
- 속도
- 일관성

## Getting Started

공식문서를 참조하자.

[Webpack - Getting Started](https://webpack.js.org/guides/getting-started/)