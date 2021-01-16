---
title: "CSS position 속성"
date: 2021-01-10
show_date: true
comments: true
read_time: false
toc: true
categories:
  - CSS
tags:
  - position
  - display
---

CSS에서 요소의 위치를 정할 수 있는 `position`속성에 대해 알아봅시다.

### Position

위에 말한 것 처럼 요소의 위치를 지정할 수 있는 속성이다. 아래와 같은 값들을 가질 수 있다.

|    값    |                의미                 |  비고  |
| :------: | :---------------------------------: | :----: |
|  static  |       기준 없음, 배치 불가능        | 기본값 |
| relative |      요소 자신을 기준으로 배치      |        |
| absolute | _위치 상_ 부모 요소를 기준으로 배치 |        |
|  fixed   |       뷰포트를 기준으로 배치        |        |
|  sticky  |     스크롤 영역을 기준으로 배치     |        |

### 위치 상 부모요소

다른 값들의 경우 특이한 의미가 없으므로 생략하겠다. *위치 상 부모요소*란 현재 요소의 상위 요소 중 `static` 값을 제외한 다른 값을 `position` 속성의 값으로 가지고 있는 요소를 지칭한다.

예를 들어 아래의 코드와 같은 화면에서 `li` 태그의 부모 요소는 `ul` 요소이지만 *위치 상 부모요소*는 `class="box-list"`로 인해 `position`속성에 기본값이 아닌 값이 정해진 `div` 요소가 되는 것이다.

예를 들어 `div`에도 포지션이 정해져 있다면 계속 상위로 올라가 window 객체를 찾게 되고 `fixed` 값과 동일하게 작동하게 된다.

```html
<body>
  <div class="box-list">
    <ul>
      <li></li>
    </ul>
  </div>
</body>
```

```css
body {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.box-list {
  position: relative;
}
```

### Stack Order - 요소 쌓임 순서

또한 각 요소들은 높이 z-index가 정해져서 보이게 되는데 우선 순위는 아래와 같다.

1. `position` 속성이 있는 요소들이 상위에
2. `position` 속성이 있는 요소 중 `z-index` 값이 높은 순위가 위로
3. `position`, `z-index` 값이 같을 경우 나중에 작성된 코드가 위로

**z-index 는 position 속성이 있어야지만 적용된다.**
