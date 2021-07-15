---
title: "Node - npm script 속성"
date: 2021-07-16
comments: true
read_time: false
toc: true
categories:
  - Node
tags:
  - npm
  - script
---

npm 을 통해 관리되는 프로젝트들은 package.json 파일을 지니게 된다.  
package.json 파일 내에는 scripts 라는 속성이 있고, 해당 속성에는 아래와 같은 지원하는 속성들이 있다.

## scripts

해당 속성들에 명령을 등록해놓을 경우 각 속성들의 동작 타이밍에 해당 명령을 통해 동작을 수행하게끔 할 수 있다.  
사용자가 커스텀하게 등록할 수도 있다.

```js
"scripts": {
  "prepare": "husky install",
  "start": "node scripts/start.js",
  "build": "node scripts/build.js",
  "test": "node scripts/test.js"
},
```

- `prepublish`: 패키지가 압축되어 배포되기 전에 실행합니다(뿐만 아니라 인자 없이 npm install 을 실행할 때에도 적용됩니다). **DEPRECATED**
- `prepare`: 인자 없이 npm install 할 때와 git dependencies 를 설치할 때, 이 둘을 패키지가 압축되어 배포되기 전에 실행합니다. 이 명령은 republish 다음 prepublishOnly 전에 실행됩니다.
- `prepublishOnly`: 패키지가 압축되어 배포되기 전에 실행하지만, npm publish 일 때만 실행합니다.
- `prepack`: tarball(tar 압축 파일)이 압축되기 전에 실행합니다(npm pack, npm publish, 그리고 git dependencies 설치시에 적용됩니다).
- `postpack`: tarball(tar 압축 파일)이 생성된 후 최종 목적지로 이동한 후에 실행합니다.
- `publish`, `postpublish`: 패키지가 배포된 후 실행합니다.
- `preinstall`: 패키지를 설치하기 전에 실행합니다.
- `install`, `postinstall`: 패키지를 설치한 후에 실행합니다.
- `preuninstall`, `uninstall`: 패키지를 삭제하기 전에 실행합니다.
- `postuninstall`: 패키지를 삭제한 후에 실행합니다.
- `preversion`: 패키지 버전을 충돌시키기 전에 실행합니다.
- `version`: 패키지 버전이 충돌된 후, commit 전에 실행합니다.
- `postversion`: 패키지 버전이 충돌된 후, 그리고 commit 후에 실행합니다.
- `pretest`, `test`, `posttest`: npm test 명령어로 실행합니다.
- `prestop`, `stop`, `poststop`: npm stop 명령어로 실행합니다.
- `prestart`, `start`, `poststart`: npm start 명령어로 실행합니다.
- `prerestart`, `restart`, `postrestart`: npm restart 명령어로 실행합니다(주의: 만약 restart 스크립트가 제공되지 않은 경우 npm restart 는 stop 과 start 스크립트를 실행합니다).
- `preshrinkwrap`, `shrinkwrap`, `postshrinkwrap`: npm shrinkwrap 명령어로 실행합니다.

## 출처

- [Suyeon Bak 님 블로그](https://medium.com/@yeon22/node-js-package-json%EC%9D%98-scripts-54e290adb5fb)
- [npm 문서](https://docs.npmjs.com/cli/v7/using-npm/scripts)
