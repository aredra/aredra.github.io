---
title: "Diary - Bitbucket issue"
date: 2021-07-01
comments: true
read_time: false
toc: false
categories:
  - Diary
tags:
  - error
  - bitbucket
---

오늘 한동안 수정할 일 없어서 접근을 하지 않고 있던 프로젝트를 수정하게 되었다.  
근데 부모듈에 접근하려 하니 갑자기 에러가...!!? (저장소는 Bitbucket 을 쓰고 있다.)

> remote: Too many invalid password attempts. Log in at https://id.atlassian.com/ to restore access.

부모듈 저장소를 직접 받아서 할 경우는 이상이 없었다. 근데 부모듈로 접근하려고 하면 발생하였다. 그리고 이 저장소에 대해서만 발생하는 것이였다.⛈

처음에는 아틀라시안 서포트 팀에 비슷한 문제가 여러건 등록되어 있길래 별로 대수롭지 않았다.  

근데 왠걸... 이걸로 3시간 가까이 날려먹을 줄은 상상도 못 했다... ⚡️

아틀라시안 서포트팀에서 제시한 해결책은 모든 세션 종료 후 재로그인 하면 된다는 것이였다. 그래도 안되면 비밀번호를 변경하라고. 하지만.. 왠걸 이게 안 됬다. 

아래와 같은 행동들 중 우여곡절 끝에 접근이 성공은 했는데 어느 부분에서 됬는지는 잘 모르겠다. 
재현하기는 너무 무섭다.. ☠️

- 재부팅
- 모든 bitbucket 세션 종료 후 아틀라시안 로그인 및 비밀번호 변경
- 키체인 삭제
- 소스트리 기본 유저 타인 계정으로 변경 후 다시 롤백