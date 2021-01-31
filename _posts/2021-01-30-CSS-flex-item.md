---
title: "CSS - Flex Item"
date: 2021-01-30
comments: true
read_time: false
categories:
  - CSS
tags:
  - flex
---

전에 Flex Container에 대해 정리하였으니, Flex Item에 대해 정리해보려 한다. 🤓

## Flex Items

Flex Container 안에 있는 요소들(items)을 지칭한다.

## Flex Items 속성

아래와 같은 속성들이 있다.

|    속성     |               의미                | 기본값  |
| :---------: | :-------------------------------: | :-----: |
|    flex     |  grow, shrink, basis 의 단축속성  |    -    |
|  flex-grow  |       item의 너비 증가 비율       |    -    |
| flex-shrink |       item의 너비 감소 비율       |    -    |
| flex-basis  |     내용(content)의 기본 넓이     |  auto   |
| align-self  | 교차축 정렬에 대한 item 개별 설정 | stretch |
|    order    |        item의 순서를 지정         |    -    |

## 예제

```html
<div class="flex-box">
  <li class="flex-item">1</li>
  <li class="flex-item">2</li>
  <li class="flex-item">3</li>
  <li class="flex-item">4</li>
</div>
```

```css
.flex-box {
  display: flex;
  flex-flow: row-reverse wrap;
  justify-content: space-around;
  align-items: center;
}
.flex-item {
  flex: 1 1 0;
}
.flex-item:first-child {
  order: 1;
  align-self: center;
}
```
