---
title: "JS - 재귀 깊이 제한"
date: 2021-07-03
comments: true
read_time: false
toc: false
categories:
  - JS
tags:
  - recursive
---

[모던 JavaScript 튜토리얼](https://ko.javascript.info/recursion) 을 보다가 깊이에 대한 제한에 대해 알게 되어 메모

다들 아시겠지만 함수가 자신 안에서 본인을 호출할 경우를 재귀 라고 한다.

잘못하면 무한루프를 돌 수 있으니 조심해야 한다.

처음 호출을 포함하여 중첩 호출의 최대 개수를 재귀 깊이라고 하며, 자바스크립트 엔진은 최대 재귀 깊이를 제한하고 있다. **`일만개`** 정도 까지는 허용하나 **`십만개`** 까지는 대다수의 엔진이 다루기 힘들다.