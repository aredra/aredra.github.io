---
title: "Nginx worker connections 오류"
date: 2021-01-14
show_date: true
comments: true
read_time: false
toc: true
categories:
  - Nginx
tags:
  - server
  - connection
  - error
---

서비스에 간헐적인 500 Error가 발생하는 상황을 보게 되었다.
처음에는 당연히 서버 에러인 줄 알았지만 서버 쪽에는 요청 자체를 받지 않은 상황이였다.

## 왜??

Application server에는 요청 자체가 기록되지 않은 상황이다 보니, 자연스레 웹서버 로그로 가니 웹서버에서 발생하는 오류임을 확인 할 수 있었다.

일단 Nginx의 기본 설정 으로 있는 부분을 확인 할 수 있었고, 최대 접속자 수 를 초과한 것으로 추정되었다.

```sh
# 접속 처리에 관한 설정을 한다.
worker_processes  1;

events {
    # 워커 프로레스 한 개당 동시 접속 수 지정 (512 혹은 1024 를 기준으로 지정)
    worker_connections  1024;
}

# 최대 동시 접속자 수 = workder_processes x worker_connections
```

## 그 외 의심 정황들

- Application-server의 최대 메모리 할당
- Nginx 유저에 대한 pid 최대 파일 오픈 수 제한

## 기타

서칭 중 정말 정리가 잘 된 블로그를 발견했다. 👍

> https://kscory.com/categories/
