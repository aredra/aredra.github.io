---
title: "CSS - 우선순위 계산"
date: 2021-01-30
comments: true
read_time: false
categories:
  - CSS
tags:
  - 우선순위
---

CSS를 사용하다 보면 특정요소를 지정하는 속성이 겹칠 수가 있다.  
그럴 경우 우선순위를 계산하여 가장 높은 우선순위를 지니는 속성을 적용하게 된다.

# 예제

아래와 같은 경우 `Hello World!`의 지칭하는 글자색 속성이 7가지나 된다. 여기서 어떤 속성이 사용되는가?

```html
<body>
  <h1 id="headline-txt" class="color-aqua" style="color: orange;">
    Hello World!
  </h1>
</body>
```

```css
div {
  color: red !important;
}
#headline-txt {
  color: green;
}
.color-aqua {
  color: aqua;
}
div {
  color: white;
}
* {
  color: lightgray;
}
body {
  color: blue;
}
```

# 우선순위 결정

위와 같은 상황에서 어떤 속성이 적용될 것인지 계산하는 방법은 아래와 같은 가이드 라인이 있다.

1. 명시도 점수가 높은 선언이 우선(명시도)
2. 점수가 같은 경우, 가장 마지막에 해석(마지막에 작성한)되는 선언이 우선(선언 순서)
3. 명시도는 '상속'규칙보다 우선(중요도)
4. `!important`가 적용된 선언 방식이 다른 방식보다 우선(중요도)

> 우선순위에는 '중요도, 명시도, 선언 순서'의 개념이 있다.

# 우선순위 계산

1. `!important`: 모든 선언 무시하고 가장 우선 (♾️ pt)
2. 인라인 선언방식: HTML style 속성을 사용 (1000 pt)
3. 아이디 선택자: # (100 pt)
4. 클래스 선택자: . (10 pt)
5. 태그 선택자: 태그 이름 (1 Pt)
6. 전체 선택자: \* (0 pt)
7. 상속: CSS Inheritance 항상 우선하지 않음 **점수 계산 X**
