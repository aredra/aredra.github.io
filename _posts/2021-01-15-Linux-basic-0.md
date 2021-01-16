---
title: "Linux Basic - 파일명 규칙"
date: 2021-01-15
comments: true
read_time: false
toc: true
categories:
  - Linux
tags:
  - basic
  - filename
  - directory
---

## 파일명 규칙

- 최대 255자 까지 가능
- 알파벳 대/소, 숫자, 마침표, 언더바, 하이픈
- 대/소문자 구별
- \ 백슬래시를 이용하여 특수 문자를 이스케이프
- 공백문자 포함시 인용부호 처리
- 숨김파일명은 마침표로 시작

## 디렉토리 구조

설치된 위치에 따라 `/usr` 혹은 `/usr/local`의 접두사를 가질 수 있음

- `/`최상위
- `/bin` 명령어 파일
- `/sbin` 시스템 명령어 - 최고 관리자
- `/lib` 라이브러리
- `/dev` 장치 파일 - 하드웨어 컨트롤
- `/etc` 환경파일
- `/var` 시스템 로그, 내역
- `/usr` 응용프로그램들의 기본적인 설치 위치
