---
title: "Web - HTTP 헤더"
date: 2021-07-12
comments: true
read_time: false
toc: true
categories:
  - Web
tags:
  - http
  - headers
---

작업 중 요청에 대한 헤더를 설정할 일이 있었는데, 문득 대소문자 여부에 대한 의문이 들었다. 전에도 한 번 찾아봤던 적이 있었는데 까먹었기에 메모해둔다.

## 결론

일단 결론부터 말하자면 대소문자를 구별하지 않는다. 😃

> 참조: [MDN - HTTP 헤더](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers)

## 기타

위에 참조된 링크를 들어가보면 알겠지만 정말 많은 헤더들이 있고 커스텀 헤더를 만들 수도 있다.  

커스텀 헤더의 경우 `X-` 를 붙여서 표기했었지만 2012년 6월 폐기되었다고 한다.  
참조: [RFC6648](https://datatracker.ietf.org/doc/html/rfc6648)

하지만 아직까지 붙여서 표기하는 경우가 많다.(구글도 그렇게 쓰는 것으로 보인다. [GCP - 헤더 지정하기](https://cloud.google.com/load-balancing/docs/user-defined-request-headers?hl=ko))