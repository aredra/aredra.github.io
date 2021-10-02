---
title: "Errors - React setting"
date: 2021-09-27
comments: true
read_time: false
toc: false
categories:
  - Error
tags:
  - error
---

React webpack 설정 중 만났던 에러 정리  
작성자의 원인을 정리한 것으로 같은 에러라도 원인은 다를 수 있다.

### Cannot find module 'react-refresh/babel'

개발서버 운영 중에 코드를 수정하면 코드 내용을 서버를 다시 안 켜도 반영해주는 HMR 을 사용하기 위해서 필요한 라이브러리는 `react-refresh` 이고 아래는 `webpack.config.ts` 에서 일부이다.

```js
// webpack.config.ts
module: {
  rules: [
    {
      test: /\.tsx?$/,
      loader: "babel-loader",
      options: {
        presets: [
          // ...
        ],
        env: {
          development: {
            plugins: [require.resolve("react-refresh/babel")],
          },
          production: {
            // production 설정
          },
        },
      },
      exclude: path.join(__dirname, "node_modules"),
    },
    {
      test: /\.css?$/,
      use: ["style-loader", "css-loader"],
    },
  ],
},
```

위 코드에서 `env` 의 `development` 설정에 `react-refresh/babel` 이 있다. 해당 모듈을 찾지 못해 에러가 난 것인데 원인은 허망했다.

> react-fresh 라이브러리를 설치한 것.

`react-refresh` 라이브러리를 설치해야 되는데 오타였다... 😳

### ReferenceError: RefreshRegRefreshReg is not defined code example

혹시 webpack.config.ts 에 아래의 2개의 플러그인이 지정되었는지 확인해보자.

```ts
// webpack.config.ts

if (isDevelopment && config.plugins) {
  config.plugins.push(new webpack.HotModuleReplacementPlugin());
  config.plugins.push(
    new ReactRefreshWebpackPlugin({
      overlay: {
        useURLPolyfill: true,
      },
    })
  );
}
```

### TypeScript with ESLint: Parsing error: The keyword 'enum' is reserved eslint

이건 `Typescript` 를 사용항여서 발생한 `ESLint` 오류로 webpack 설정은 아니고 실제 동작에는 이상은 없지만 빨간줄은 보고 싶지 않기에 수정하였다.  
`eslint` 설정에서 `"parser": "@typescript-eslint/parser"`를 추가해보자.

```json
// .eslintrc
{
  "env": {
    "es6": true,
    "browser": true,
    "node": true
  },
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "parser": "babel-eslint",
    "sourceType": "module",
    "allowImportExportEverywhere": true,
    "ecmaFeatures": {
      "jsx": true,
      "modules": true
    }
  },
  "extends": ["plugin:prettier/recommended"]
}
```
