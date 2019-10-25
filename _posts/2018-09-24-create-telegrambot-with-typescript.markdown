---
layout: post
comments: true
title:  "타입스크립트로 텔레그램 챗봇 만들기"
date:   2018-09-24
author: "J911"
categories: server
cover: "/assets/post-assets/2018-09-24-create-telegrambot-with-typescript/cover.png"
---
텔레그램을 이용한 투두(TODO)앱을 만들고싶어서 텔레그램 챗봇을 타입스크립트와 함께 쓰는 법을 공부해 보았다.

## 텔레그램 봇 토큰 발행하기
챗봇을 생성하기 위해서는 `@botFather`에서 메세지를 보내어 토큰을 할당받아야한다.    
`@botFather`는 텔레그램에서 챗봇 토큰을 발행해주는 챗봇이다.

![botFather-step-1](/assets/post-assets/2018-09-24-create-telegrambot-with-typescript/botFather-step-1.png)

`@botFather`과 대화를 시작하면 전체 사용법을 알려주는데 우리는 새로운 토큰을 발행할 것이니 `/newbot`이라고 입력해주자

![botFather-step-2](/assets/post-assets/2018-09-24-create-telegrambot-with-typescript/botFather-step-2.png)

그러면 챗봇을 부를 이름을 정하라고 나온다. 원하는 이름을 답장으로 보내주자

![botFather-step-3](/assets/post-assets/2018-09-24-create-telegrambot-with-typescript/botFather-step-3.png)

이제는 봇의 `username`을 정해달라고 한다. 여기서 주의할 점은 `username`의 **마지막은 bot**으로 끝나야한다.

![botFather-step-4](/assets/post-assets/2018-09-24-create-telegrambot-with-typescript/botFather-step-4.png)

자 이렇게 토큰을 받았다면 node js에서 코딩을 할 준비하자.

## typescript 준비하기
우선 프로젝트 폴더를 만들어주자.
```
$ mkdir my-telegram-bot
$ cd my-telegram-bot
$ npm i -y
```

그리고 타입스크립트 사용을 위한 모듈들도 설치해주자.
```
$ npm i -D ts-node typescript @types/node
```

그리고 간단하게 `tsconfig`도 작성하자. 
{% gist J911/55de591d95b8123d3d0f8576bff87112 %}

자 시작할 준비는 모두 끝났다.

## 본격 개발시작
우선 `telebot`을 설치해주어야 한다.

```
$ npm i -S telebot
```
그리고 /service 디렉토리를 작성하고 /service/bot-service.ts를 만들어주자.

```
$ mkdir service
$ touch bot-service.ts
```

{% gist J911/0a61d039b9b2f5d5735371ec3a3d26b4 %}

`telebot`을 불러오고 싱글톤으로 클래스를 하나 만들어주었다.

```
this.bot = new Telebot(token);
```
그리고 다음과 같이 토큰을 통해 `Telebot` 객체를 만들어야 하는데, `yaml` 로더를 통해 `token`을 불러왔다.

/environment/environment.yml

{% gist J911/5a184230cf043d00a39dc87cf6df14a7 %}

/environment/environment-handler.ts

{% gist J911/01250bf2af2f01eba4cd74aa600a7648 %}

그러면 다음과 같이 코드를 작성할 수 있다.

{% gist J911/fbb51617c9eee26b86a1cd26718297d9 %}

여기서는 간단하게 메세지를 받고 해당 사용자에게 답장을 하는 로직을 작성해 볼 것이다.

간단하게 `bot.on(event, function)` 메서드로 이벤트를 받아올 수 있다.


- \*: any type of message
- text: text message
- audio: audio file
- voice: voice message
- document: document file (any kind)
- photo: photo
- sticker: sticker
- video: video file
- ... 

더 많은 이벤트는 [npm문서](https://www.npmjs.com/package/telebot)를 참고하자.

그래서 다음과 같이 `이벤트 리스너`를 작성할 수 있다.

{% gist J911/a2c5261305852f38d6a790bea931d42e %}

여기에서 들어오는 msg는 송신측의 이름, 전송 정보 등을 담고있는 객체이다.

반대로 봇이 메세지를 전송하기 위해서는 `bot.sendMessage(chatId, msg)` 메서드를 사용한다. 이 메서드 이외에도 msg객체 안에 포함된 reply 메서드를 사용할 수도 있다.

{% gist J911/0f79a0484164daad1e3526498de575ff %}

그리고 챗봇을 실행시키는 `bot.start()`메서드를 추가하여 코드를 완성시키면 됩니다.

{% gist J911/29818fd807805693a6587a544220b557 %}

나는 간단하게 텔레그램에서 온 메세지를 데이터베이스에 저장하고 http api로 호출해 불러올 수 있는 간단한 프로그램을 작성해보았다.

[https://github.com/J911/telegram-regist-bot](https://github.com/J911/telegram-regist-bot)에서 확인할 수 있다.