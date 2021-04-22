---
title: "Cookie - SameSite"
date: 2021-04-21
comments: true
read_time: false
toc: true
categories:
  - Web
tags:
  - cookie
  - samesite
---

`Chrome` 브라우저에서 `SameSite` 관련하여 `Cookie`가 차단되어 로컬 개발 환경에서 정상동작 하지 못하는 일이 발생하였다.

## SameSite 속성

상기 속성은 예전부터 언급은 되고 있는 속성이였다 한다.

> 작년에 크롬 80버전 때 이슈가 되어 찾아봤을 때 처음 알았다... 💦

요청의 `Host`와 `Origin`의 도메인에 일치 여부에 따라 저장을 하게 할 것인지에 대한 여부를 정하는 속성이라고 할 수 있다.  
크롬 80버전에서는 도메인에 대해서만 확인을 하였던거 같은데 이게 89버전에서 `Schemeful`로 변경되었다!

> [크롬 Feature: Schemeful same-site](https://www.chromestatus.com/feature/5096179480133632)

## 문제점

보통 Local에서는 http 로 개발하는 일이 많았는데 프로토콜을 비교하게 되며, 정상동작을 안 하게 되었다.

> 확실하지 않지만 쿠키의 도메인 역시 명시하지 않으면 Host의 풀 도메인으로 설정되며 서브도메인에 대해 허용이 안되는 것 같다.

Local dev-server를 https로 설정을 바꾸는 방법이 존재하지만 시간상의 이유로 개발의 요청주소를 http로 변경 및 쿠키의 도메인을 명시하며 문제는 일단락이 되었다.

## 사용이유

그렇다면 사용이유는 무엇일까??  
보안적인 부분의 강화이며, 대표적으로는 `CSRF(Cross-Site Request Forgery)` 방어이다.  
기존의 경우 다른 Host에서 생성된 쿠키도 저장하여 요청에 셋팅되어 보내졌으며, 이를 방어하는 것은 개발자의 영역이였으나, 이를 클라이언트 브라우저에서 자체적으로 막는 것이다.
