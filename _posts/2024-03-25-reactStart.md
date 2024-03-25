---
layout: post
title:  "react.js 설치 및 폴더구조"
author: "서민기"
tags: [how to start react]
---



# React.js 설치하기

1. Node.js 설치하기
    - 자바스크립트 런타임
    - react 설치 최소 버전 10 이상

2.  npx create-react-app . (or project name)
    - npm vs npx
        - npm 은 내 컴퓨터 안에 글로벌한 공간에 모듈을 설치 (각 프로젝트 마다 같은 모듈)
        - npx 최신 버전 설치
    - CRA 
        - 보일러플레이트
        - 바벨, 웹팩 X

3. npm start
    - 최초 세팅 포트 3000

---

# 타입스크립트로 프로젝트 만들기

```
npx create-react-app project-name --template typescript
```

# 기존 프로젝트 TS 추가

## npm 패키지로 적용
```
npm install --save typescript @types/node @types/react @types/react-dom @types/jest
```

<br>

## tsconfig.json 프로젝트 설치 및 컴파일 옵션 변경
```
npx tsc --init
```

## 확장자 변경
js, jsx -> ts, tsx

[https://any-ting.tistory.com/93]
[https://bitbucket.prodyna.com/users/ple/repos/react-typescript-guide/browse]

---

# Boilerplate
 
## 보일러플레이트란?
    - 기본적인 골격을 미리 만들기
    - 단순한 노등을 없애주는 것

# CRA webpack 설정

```
// 숨겨진 설정 파일 표시
// 한번 실행시 이전 상태로 돌아갈 수 없음
npm run eject
```

[https://maxkim-j.github.io/posts/cra-webpack-config/]

---

# CRA 폴더 구조

## node_modules
    - 외부 라이브러리 종속성
    - package.json 에 기록된 모듈

## package.json
    - 프로젝트의 정보
    - 의존성 모듈
    - 스크립트

## package-lock.json
    - package.json 보완
    - 모든 패키지의 정확한 버전과 의존하는 다른 패키지, 해당 패키지의 하위 의존성에 대한 정보

## public
    - 정적(static) 파일들
    - html, css, js
    - 서버 측에서 별도의 처리 없이 그대로 사용

## index.html
    - 웹사이트의 기본 페이지
    - SPA

## src
    - spa의 주요 개발 영역
    - 컴포넌트, 모듈, 리소스

## App.js
    - 메인 컴포넌트 (index.html 렌더링)


## index.js
    - 웹팩에서 애플리케이션의 시작점
    - 초기 설정

## .gitignore
    - git 으로 관리하지 않을 파일


---

# JSX

```javascript
const element = <h1>Hello, world!</h1>
```

## 자바스크립트 확장문법
    - 자바스크립트의 모든 기능 포함
    - 렌더링 로직이 UI 로직과 연결
    - 마크업과 로직을 넣어 인위적으로 분리