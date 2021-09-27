---
title: "Diary - Java encoding issue"
date: 2021-07-04
comments: true
read_time: false
toc: false
categories:
  - Diary
  - Java
  - Error
tags:
  - error
  - encoding
---

`Java` 어플리케이션에서 이메일을 발송하는 기능이 있었는데 특정 도메인에서 메일 제목이 `??????` 로 나오는 현상을 발견했다!! 😵

## 현상

1. `staging domain` 에서만 메일 발송 시 수신함의 메일 제목의 한글이 ????? 로 표기가 된다.
2. 본문의 한글은 잘 표기된다.

## 원인

1. **staging domain** 의 Java 어플리케이션은 `Docker` 에서 구동을 하고 있었고, Docker 컨테이너의 **Locale, Language** 가 따로 설정되지 않아 있었다.
2. Java 의 `MimeMessage` 클래스의 `setSubject` 메서드를 사용했는데 이때 타입을 지정해주지 않았다.
3. 본문의 경우는 `charset=utf-8` 을 지정해주고 있었다.

## 해결
- `Docker container` 에 **ko_KR.UTF-8** 을 명시함