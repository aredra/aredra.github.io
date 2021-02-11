---
title: "JS getter, setter"
date: 2021-02-11
comments: true
read_time: false
categories:
  - JS
tags:
  - getter
  - setter
---

JS의 ES5에서도 getter, setter는 존재하였으나 ES6로 오면서 스펙의 변동이 있었다.  
이에 대해 간략히 정리한다.

## ES5 - getter, setter

프로퍼티의 속성의 구조를 지니고 있으며, 아래와 같이 사용한다.

```js
const developer = {};
Object.defineProperty(developer, "skills", {
  get: function () {
    return ["js", "react", "vue", "nginx"];
  },
});
console.log(developer.skills);
```

`developer.skills.get()`과 같이 함수 형태로 호출하게 될 경우 에러가 발생한다.

## ES6 - getter, setter

함수 앞에 get, set 예약어로 작성을 할 경우 getter, setter가 생성된다.

```js
const developer = {
  skills: ["js", "react", "vue", "nginx"],
  get getSkills() {
    return this.skills;
  },
  set setSkills(param) {
    this.skills = param;
  },
};
console.log(developer.getSkills);
developer.setSkills = ["js", "react", "vue", "nginx", "java"];
console.log(developer.getSkills);
```

## 차이

ES6로 넘어오면서 프로퍼티에 대한 종속을 벗어나게 되었으며, 작성이 용이하게 되었다.
