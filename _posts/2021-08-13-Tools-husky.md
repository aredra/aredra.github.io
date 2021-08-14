---
title: "Node - husky, lint-staged"
date: 2021-08-13
comments: true
read_time: false
toc: true
categories:
  - Node
  - Library
tags:
  - husky
  - lint-staged
---

`CI/CD` 를 설정하는 과정에 있어서 저장소 코드를 build 할 때 lint 에서 에러가 발생하는 부분이 있었다.  
`CI/CD` 파이프라인이 깨져서 다시 돌릴 경우 시간이 아까워서 `husky` 와 `lint-staged` 를 동록하게 되었다.

## husky

`NPM` 으로 관리되는 프로젝트에서 `git hooks` 를 쉽게 다룰 수 있게 해주는 라이브러리이다.  
`git hooks` 이 낯설 수도 있는데 FE 프레임워크의 훅들과 똑같다고 생각하면 된다. 등록을 할 경우 특정 시점에 원하는 동작을 할 수 있게끔 해준다! 👍

간단하게 아래와 같은 `git hooks` 들이 있고 `husky` 없이 직접 사용하는 방법도 어렵지 않으니 궁금하시다면 갓:구글 님께 문의를 넣어보자.

|          훅          |                      설명                       |
| :------------------: | :---------------------------------------------: |
|     `pre-commit`     |             commit 을 실행하기 이전             |
| `prepare-commit-msg` |  commit 메세지를 작성하고 편집기를 실행하기 전  |
|     `commit-msg`     |     commit 메세지 등록 후 commit 완료 직전      |
|    `post-commit`     |             변수, 함수명 일괄 변경              |
|     `pre-rebase`     |                 rebase 하기 전                  |
|    `post-rewrite`    | rebase, merge 등 commit 을 변경하는 명령실행 후 |
|     `post-merge`     |                  merge 완료 후                  |
|      `pre-push`      |         데이터를 remote로 push 하기 전          |

이전 버전에서는 `package.json` 이나 `.huskyrc` 와 같은 파일에서 간단하게 작성하면 돌아갔었는데 버전 업 되면서 `sh` 을 작성하도록 변경되었다.

#### husky Version ~4.x

```json
// package.json

{
    "scripts": {
        "install": "node husky install",
    }

      ...
    "husky": {
      "hooks": {
          "pre-commit": "echo 'Hello'",
          "pre-push": "npm test"
      }
    }
}
```

#### husky Version 5~

자세한 내용은 공식사이트를 참조하자 어렵지 않다. [husky npm 사이트](https://www.npmjs.com/package/husky)

```json
// package.json

{
    "scripts": {
      "prepare": "husky install",
    }
      ...
}
```

## lint-staged

스테이지에 올라간 파일들만 lint 해주는 라이브러리다 [lint-staged - Github](https://github.com/okonet/lint-staged)  
사용법이 어렵지 않다. 인터넷이 된다면 튜토리얼을 따라하면 쉽게 적용할 수 있다.

```json
// package.json

{
  ...

  "lint-staged": {
    "*.js": ["eslint --fix", "prettier --write"]
  }
}
```
