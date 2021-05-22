---
title: "Vue - Lifecycle"
date: 2021-05-22
comments: true
read_time: false
toc: true
categories:
  - Vue
tags:
  - lifecycle
---

Vue 를 나름 자주 사용하는 편인데 글이 없는 것 같아 Lifecycle 관련하여 메모해본다.

## Vue Lifecycle

Vue 의 인스턴스, 컴포넌트가 생성되고 사라지기는 과정이라고 할 수 있고 그 과정 속에 아래와 같은 훅들이 있다.

![vue-lifecycle](/assets/images/vue-lifecycle.png)

> 출처: Vue 공식 사이트

1. beforeCreate
    - 가정 먼저 실행되는 훅이며, Vue instance 초기화 직후 발생하게 되어 인스턴스 접근 불가

2. created
    - 인스턴스가 `DOM` 에 추가가 된 상태는 아니나 `data` 및 `computed` 등 접근 가능
    - 초기 data 셋팅을 해당 단계에서 많이 함

3. beforeMount
    - 템플릿 랜더링은 된 상태이나 `DOM` 에 추가가 된 상태는 아님
    - `SSR` 렌더링 시 호출 되지 않는다.

4. mounted
    - `가상DOM` 내용이 `DOM` 에 추가 완료, `$el` 및 모든 요소 접근 가능
    - 보통은 자식 컴포넌트가 마운트가 되고 부모가 마운트 됨
    - 위 사항을 항상 보장하지 않으므로 `$nextTick` 사용
    - `SSR` 렌더링 시 호출 되지 않는다.

5. beforeUpdate
    - `가상DOM` 를 리랜더링 하기 전 상태
    - data 값 변동으로 다시 리렌더링 되기 직전
    - 해당 훅에서 data 변동은 다시 리랜더링 되지 않는다.

6. updated
    - 변경된 `data` 가 `DOM` 에 적용된 상태 
    - 해당 훅에서의 `data` 변동은 무한루프를 야기할 수 있다.
    - `mounted` 와 마찬가지로 하위 컴포넌트의 리랜더링을 보장하지 않으므로 주의

7. beforeDestroy
    - 인스턴스가 제거되기 전 상태
    - 제거 된 상태이므로 온전한 기능을 다 쓸 수 있음
    - `SSR` 에서는 호출 되지 않음

8. destroyed
    - 인스턴스가 제거되고 난 후
    - `SSR` 에서는 호출 되지 않음
