---
layout: post
comments: true
title:  "타입스크립트와 JWT로 인증 API 구축하기"
date:   2018-09-27
author: "J911"
categories: server
cover: "/assets/post-assets/2018-09-27-build-jwt-auth-api-with-typescript/cover.png"
---
타입스크립트와 `JWT(JSON Web Token)`을 사용해서 간단한 express API 서버를 만들어보고자 한다.

## 프로젝트 준비

일단 다음과 같은 프로젝트 디렉토리를 생성했다.

```
.
└── src
    ├── api
    ├── controller
    ├── environment
    ├── interface
    ├── middleware
    └── models
```

## 타입스크립트 준비하기 

그리고 타입스크립트를 사용하기 위해 의존성 모듈 설치와 `tsconfig.json`를 등록해주자.

```
$ npm init
$ npm i -D ts-node typescript @types/node
```

/tsconfig.json

{% gist J911/0b1907ac74503b4dd7e64ce57a81f2dd %}

## 기본 express 서버 구축하기

그리고 간단한 express 서버 골격을 만들어주자.

```
$ npm i -S express body-parser
```

/src/app.ts
{% gist J911/1d66da7fbb8f5fdcc13affb0026c970a/aa759bdfe34cb5ca406724fe4b1297e226171a1a %}

/src/index.ts

{% gist J911/972932b802bd1185c22882eb88e75f31/76d64a8ed4e3a97ad20230e9fcb2c18e6b449f4c %}

이후 다음과 같이 명령을 내리면 서버가 실행된다.

```
$ ts-node src // server is running
```

여기서 개발 로직과 관련이 없는 포트번호 등과 같은 프로퍼티는 `yaml`로 따로 분리해 사용할 것이다.

`yaml`을 로드하기 위해 `js-yaml` 모듈을 설치해주자.

```
$ npm i -S js-yaml
```

그리고 environment 디렉토리 아래에 `environment.yml`을 작성해주자.

/src/environment/environment.yml

{% gist J911/b83e3245aa50386b6500e6c97da5cca9/24f3c446781c2926cbeca0fde7ac22ede9d9fdd2 %}

그 이후 yml에서 값을 읽어오기 위해 핸들러를 만들어주자.

/src/environment/environment-handler.ts

{% gist J911/50a485b222b3221094be1e821e274c19/097cc834f97cf8980a12673d34690f03d67601d3 %}

이렇게 따라오면 아까 작성한 index.ts 코드를 다음과 같이 수정할 수 있다.

/src/index.ts

{% gist J911/972932b802bd1185c22882eb88e75f31/f8dad12d37dfc8da043d07eaf23df11b26cc39b7 %}

## 데이터 베이스 등록하기

이제는 회원을 관리할 수 있는 데이터베이스를 등록해보자.

이번 포스트에서는 mongoDB를 사용할 것이므로 `mongoose` 설치해주자.

```
$ npm i -S mongoose
```

그 후 models 폴더에 스키마와 인터페이스를 구성해주자.

{% gist J911/7edfece8b7a6512d1fe55d398c0f29f0/8d6d10353dfbafed30c2b2da67563cabf5ca3522 %}

그리고 environment.yml에 데이터베이스 커넥션 정보도 추가해주자.

{% gist J911/b83e3245aa50386b6500e6c97da5cca9/4dcb8e0a79fdfe9dc99bb0b6a593c9df1174ee0a %}

마지막으로 app이 실행될 때 데이터베이스도 연결시켜주자.

{% gist J911/1d66da7fbb8f5fdcc13affb0026c970a/e87906d2da1212c341025ac999f6371ed1891035 %}

## 본격 API 설계하기

이제 api 라우터를 만들어 볼 텐데, 앞으로 만들 라우터가 많지는 않겠지만 추후 추가될 수있는 라우터가 생길 수 있으므로 조금 번거롭지만 `router-abstract`를 만들어 활용하도록 하자.

/src/router-abstract.ts

{% gist J911/5410ab1bc489fbf97f86efc8eeac9205/bd2e1bece759c44f1d86e55dd8353b178d744728 %}

본격적으로 api를 만들어 볼 차례다.

api 디렉토리에 api를 라우팅해 줄 index.ts와 auth-route.ts, account-route.ts를 작성하자.

/src/api/auth-route.ts

{% gist J911/574ce42337961c9bc750181e02496034/e172f28ecdf45edb226dc34c4865062ad9d3cb83 %}

/src/api/account-route.ts

{% gist J911/f8c27d09432f29e0822cb331eaf98665/4d1a730a7f97f345cd8a3ad78093060aed65cd68 %}

/src/api/index.ts

{% gist J911/16d468907f207a2688c7444a689bfa0f/211e81280a397776c1faa8c9b293a403372bcdfc %}

/src/app.ts

{% gist J911/1d66da7fbb8f5fdcc13affb0026c970a/74c55ce2d1f062e6f6f8f6c78b6dd486dc6ec114 %}

간단하게 `sign-in`, `sign-up` 라우터를 추가했다.

이제 실제 동작을 시킬 수 있도록 컨트롤러를 설계할 것이다.

각 컨트롤러는 인터페이스에 맞는 타입을 리턴시킬 수 있도록할 것이다. 이렇게 할 경우 유지보수 작업이나 대규모 프로젝트를 할 경우 정말 편해진다.

인터페이스를 등록하자.

/src/interface/response-interface.ts

{% gist J911/be3f610f6175f74968f00182a8103400/ddfa5051fea580dd841b519dde5a6a80f2c7a526 %}

그리고 account의 비밀번호를 암호화하고 복호화할 수 있는 모듈을 설치하자.

이번 포스팅에서는 `bcrypt`를 사용할 것이다.

```
$ npm i -S bcryptjs
```
이제 account 를 관리할 account 컨트롤러를 작성하자.

간단하게 계정 생성, 삭제, 변경 등으로 로직을 작성했다.

/src/controller/account-controller.ts

{% gist J911/8118f481acc20166d76ee383c843ecac/b3ce7bffccb8b9b3ac40559877802ae7f1e5b292 %}

이제 로그인 로그아웃 등을 관리할 auth 컨트롤러도 만들어주자.

여기서 드디어 JWT모듈이 들어간다.

다음과 같이 설치하자.

```
$ npm i -S jsonwebtoken
```
jwt 모듈은 토큰을 생성하는 `jwt.sign()`메서드와 토큰의 유효성을 체크하는 `jwt.verify()` 메서드를 제공한다.

여기서 사용하는 `jwt.sign()`는 토큰을 생성할 때 필요한 secret 문자열을 필요로 한다. 이것도 마찬가지로 `environment.yml`에 등록해 사용하자.

{% gist J911/b83e3245aa50386b6500e6c97da5cca9/c3bbbd21f4e8c7cf76f30979cdf452f16da4e822 %}

`jwt.sign()`으로 토큰을 발행하여 반환해 주면 클라이언트는 해당 토큰을 브라우저의 로컬 스토리지 혹은 세션에 저장을 하고 인증이 필요한 요청이 있을 때 요청 헤더에 token을 넣어 보내 인증을 시킬 것이다.

이를 활용해 다음과 같이 컨트롤러를 작성하자. 

/src/controller/auth-controller.ts

{% gist J911/6c82d62d5d3704fed52cb6c0df250a4d/c9c33efdf227dfab2ae73eb2f3ee41d370170a6f %}

이제 route를 완성시켜주자.

/src/api/auth-route.ts

{% gist J911/574ce42337961c9bc750181e02496034/be043889314ac44fb01e2a00d14e9e12162ea59a %}

/src/api/account-route.ts

{% gist J911/f8c27d09432f29e0822cb331eaf98665/eca9fa089ec860110be79fb0a889d2b20581860b %}

이제 마지막으로 header에 token을 넣어서 보낸 사용자만 account api에 접근할 수 있도록 미들웨어를 등록해주자.

/src/middleware/index.ts

{% gist J911/9b32246f702db2d00500e9563eb35022/4f4d3497398c9ef2bcfe0be611cfd8dab3e0dc52 %}

/src/middleware/auth-middleware.ts

{% gist J911/7189e237b955e67f191615966c6010fa/2a520fba5dbf0accf14e516f137ff18369cc6127 %}

## 이 프로젝트에서 작성된 api의 명세

### Auth

#### Sign In
로그인을 통해 JWT 인증토큰을 받아온다.

- uri: /api/v1/auth/sign-in
- method: POST
- request: userName, password
- response: token, username
- status: 200, 400, 403, 500

#### Sign Up
새로운 사용자를 등록한다.

- uri: /api/v1/auth/sign-up
- method: POST
- request: userName, password
- status: 201, 500, 409

### Accounts

#### Get UserName
사용자 정보를 가져운다.

- uri: /api/v1/accounts/${userId}
- method: GET
- request header: x-access-token
- response: userId, userName
- status: 200, 401, 403, 500

#### Update Password
사용자 비밀번호를 변경한다.

- uri: /api/v1/accounts/${userId}/password
- method: PUT
- request header: x-access-token
- request: userId, password, newPassword
- status: 204, 400, 401, 403, 500

#### Update UserName
사용자 이름을 수정한다.

- uri: /api/v1/accounts/${userId}/username
- method: PUT
- request header: x-access-token
- request: userId, password, newUserName
- status: 204, 400, 401, 403, 409, 500

#### Delete User Account
비밀번호 인증을 통해 사용자를 삭제한다.

- uri: /api/v1/accounts/${userId}
- method: DELETE
- request header: x-access-token
- request: userId, password
- status: 204, 400, 401, 403, 500


## 완성된 프로젝트

완성된 프로젝트는 [https://github.com/J911/simple-jwt-api-with-typescript](https://github.com/J911/simple-jwt-api-with-typescript) 에서 볼 수 있다.