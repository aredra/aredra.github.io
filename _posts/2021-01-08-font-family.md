---
title: "글꼴 계열 관련 메모"
date: 2021-01-08
show_date: true
comments: true
read_time: false
toc: true
categories:
  - CSS
tags:
  - font
---

CSS나 font의 글꼴은 계열을 가지고 해당 계열에 대해 자꾸 잊어먹어서 메모하려 한다. 🧐

```css
body {
  font-size: 12px;
  font-family: "Roboto", "Segoe UI", "Helvetica Neue", sans-serif;
}
```

css에서는 위와 같이 `font-family` 속성을 통해 폰트 모양(?)를 설정할 수 있고 마지막에 해당하는 계열을 다 찾지 못 하였을 경우 비슷한 계열을 가져오게 하며 계열들은 아래와 같이 확인 하면 된다.

|    계열    |     의미      |
| :--------: | :-----------: |
|   serif    |  바탕체 계열  |
| sans-serif |  고딕체 계열  |
| monospace  | 고정너비 계열 |
|  cursive   |  필기체 계열  |
|  fantasy   |   특이 계열   |
