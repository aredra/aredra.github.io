---
title: "JS - Unicode"
date: 2021-01-25
comments: true
read_time: false
categories:
  - JS
tags:
  - unicode
---

JS에서는 `\u0031`와 같은 방식으로 유니코드를 사용이 가능하다.  
본래 ES5에서는 아래와 같은 범위 밖에 지원하지 않았으나 4자리 이상을 2가지 유니코드 값을 이어 붙여서 지원하는 방식을 제공했고 이를 `Surrogate pair`라고 한다.

> 유니코드 값 범위 U+0000 ~ U+FFFF

ES6부터는 그냥 `\u{fffff}`와 같이 표기를 하며 4자리 이상을 지원해준다.

**Emoji의 경우는 각 단말기마다 형태가 다를 수 있다.**
