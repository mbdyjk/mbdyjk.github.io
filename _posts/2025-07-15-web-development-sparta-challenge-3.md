---
title: "웹개발 수업 복습 - 스파르타 챌린지반 학습자료(3)"
date: 2025-07-15
categories: [React]
tags: [React]
---

CRA, Vite 없이 React 개발 환경 구축하는 과정을 정리한다.
Webpack과 Babel을 이용해 번들러와 트랜스파일러를 직접 설정하여 React 프로젝트를 구축한다.

1. 프로젝트 폴더 생성, 프로젝트 초기화
 
2. 필요한 패키지 설치
```bash
npm install react react-dom
npm install --save-dev @babel/core @babel/cli @babel/preset-react webpack webpack-cli webpack-dev-server babel-loader html-webpack-plugin clean-webpack-plugin dotenv-webpack cross-env
```

3. 프로젝트 구조 생성
```text
no-cra/
├── src/
│   ├── app.js
├── index.html
├── .babelrc
├── webpack.config.js
├── .env.development
├── .env.production
└── package.json
```

4. HTML 파일 생성
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>No CRA</title>
  </head>
  <body>
    <div id="root"></div>
  </body>
</html>
```
root id의 DOM 컨테이너를 렌더링, Webpack 번들링으로 React를 로드하므로 `<script>` 태그는 포함하지 않음 

5. `src/app.js`에 간단한 React 컴포넌트 작성
```js
import React from "react";
import ReactDOM from "react-dom/client";

const App = () => {
  return <h1>Hello, World!</h1>;
};

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<App />);
```

6. Babel 설정
.babelrc 파일 생성 후 작성
```json
{
  "presets": ["@babel/preset-react"]
}
```
Babel은 JSX를 일반 JavaScript로 변환하는 역할 수행,
@babel/preset-react는 JSX와 React 문법을 처리하도록 Babel에 지시

7. Webpack 설정
webpack.config.js 파일 생성 후 작성
```js
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
const DotenvWebpack = require("dotenv-webpack");

const mode = process.env.NODE_ENV || "development";

module.exports = {
  mode,
  entry: "./src/app.js",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "bundle.[contenthash].js",
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
        },
      },
    ],
  },
  plugins: [
    new CleanWebpackPlugin(),
    new HtmlWebpackPlugin({
      template: "index.html",
      filename: "index.html",
    }),
    new DotenvWebpack({
      path: `./.env.${mode}`,
    }),
  ],
  devServer: {
    static: {
      directory: path.join(__dirname, "dist"),
    },
    port: 9000,
    open: true,
    hot: true,
  },
};
```

Webpack은 모듈을 번들링하고, `babel-loader`를 사용해 JSX 처리

위 설정 설명:
- entry: 번들링 진입점
- output: 번들 파일 출력, [contenthash]로 캐싱 문제 방지
- module.rules: babel-loader로 js 파일 처리
- plugins:
  - CleanWebpackPlugin: 빌드 전 dist 폴더 정리
  - HtmlWebpackPlugin: index.html을 기반으로 번들 파일 자동 삽입
  - DotenvWebpack: 환경 변수 로드
- devServer: 개발 서버 설정

8. 환경 변수 설정
.env.development와 .env.production 파일을 생성해 환경별 설정 관리

9. package.json 설정
```json
{
  "name": "no-cra",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "build": "cross-env NODE_ENV=production webpack",
    "start": "cross-env NODE_ENV=development webpack serve"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0"
  },
  "devDependencies": {
    "@babel/cli": "^7.24.8",
    "@babel/core": "^7.24.8",
    "@babel/preset-react": "^7.24.7",
    "babel-loader": "^9.1.3",
    "clean-webpack-plugin": "^4.0.0",
    "cross-env": "^7.0.3",
    "dotenv-webpack": "^8.1.0",
    "html-webpack-plugin": "^5.6.0",
    "webpack": "^5.93.0",
    "webpack-cli": "^5.1.4",
    "webpack-dev-server": "^5.0.4"
  }
}
```

cross-env로 Windows와 Unix 환경에서 NODE_ENV 설정을 일관되게 처리

10. 환경 변수 사용
.env.development, .env.production에 선언, 개발 모드(npm start), 프로덕션 모드(npm run build)에서 다르게 동작
