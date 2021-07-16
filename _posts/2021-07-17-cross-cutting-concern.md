---
title: "횡단관심사 - Cross-Cutting Concerns"
date: 2021-07-17
comments: true
read_time: false
toc: true
categories:
  - Programming
tags:
  - cross-cutting concerns
---

`HOC` 를 보다가 모르던 부분을 알게 되어 작성해보려고 한다.

## 횡단관심사 란?

영문으론 `Cross-Cutting Concerns` 이라고 하며, 간단하게 말하자면 각 기능(도메인)에서 공통적으로 사용되는 영역이다.  
예를 들자면 인증, 로깅, 트랜잭션 처리 등으로 볼수 있다.

## AOP, HOC

`AOP(aspect-oriented programming)` 는 이러한 횡단관심사의 분리를 허용함으로 모듈성을 증가시키기는 것이 목적인 프로그래밍 패러다임이며, BE Spring 프레임워크를 사용하신 분들이라면 한번씩은 다들 들어보셨을 거라고 생각한다.

사실 BE 에서만 사용하는 언어라고 할 수 없지만 FE 쪽에서는 `HOC` 가 횡단관심사를 핸들링하기 좋은 예라고 할 수 있다.

## 참조

- [위키백과 - 횡단관심사](https://ko.wikipedia.org/wiki/%ED%9A%A1%EB%8B%A8_%EA%B4%80%EC%8B%AC%EC%82%AC)
