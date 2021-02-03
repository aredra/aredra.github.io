---
title: "웹 페이지 로드 과정"
date: 2021-02-03
comments: true
read_time: false
categories:
  - Web
tags:
  - 동작
---

웹 페이지가 로드 되는 과정에 대해 간략히 정리해보자

![navigation-api](/assets/images/navigation-timing-overview.png)
출처: [W3C](https://www.w3.org/TR/navigation-timing/#processing-model)

### Prompt for unload

이전 페이지문서가 unload되는 시점이다. 앞으로가기 뒤로가기 등 현재 페이지를 벗어하려 하는 단계

### Redirect

HTTP에서 페이지 재전송이 있을 경우 발생하는 단계

### AppCache

HTTP 요청에 대한 리소스의 캐시 존재를 확인하고, 없을 경우 응답으로 리소스를 받아오기를 시작하는 단계

### DNS

요청 주소에 대한 도메인에 대해 DNS로 IP검색을 하는 단계  
IP로 요청을 하거나, 운영체제내에 명시되어 있으면 하지 않는다. `/etc/hosts`

### TCP

요청하는 서버와 TCP 연결을 하는 단계  
캐시되어 있는 리소스나 연결이 필요없는 경우 발생하지 않음

### Request

TCP 연결 후 요청을 시작하는 단계

### Response

요청에 대한 응답을 수신하는 단계

### Processing

브라우저가 웹페이지를 만드는 단계

### OnLoad

리소스 다운로드가 되고 있는 단계  
완료되면 load 이벤트가 발생한다.
