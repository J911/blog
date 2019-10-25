---
layout: post
comments: true
title:  "Node js에서 타입스크립트와 함께 yaml/yml사용하기"
date:   2018-09-28
author: "J911"
categories: server
cover: "/assets/post-assets/2018-09-28-using-yaml-in-typescript/cover.png"
---
`yaml`은 데이터를 가볍게 담고있는 포맷입니다. 정말 직관적인 포맷으로 문자열, 숫자, 배열, 리스트 등의 자료형도 지원을 합니다.

이것을 타입스크립트와 합께 사용하는 방법을 익혀봅시다.

일단 간단한 `yaml` 파일을 작성해봅시다. `yaml` 파일을 `.yml` 확장자를 사용합니다.

account.yml
{% gist J911/411d10851844adf97580559836497cfa/revisions#diff-403e7043c5a93ff543f7c9cc459ab36b %}

이렇게 작성한 `yaml`은 `js-yaml` 모듈을 통해 타입스크립트에서 로드할 수 있습니다.

```
$ npm i -S js-yaml
```
app.ts

{% gist J911/53d67184d4da591992ed971ff679c78c/fa22f7699eea252501ccc01429b87bb272d594de %}

이렇게 가볍게 불러 사용할 수 있다.

나와 같은 경우는 express등 서버환경을 설정할 때 yaml을 자주 사용을 하는데 보통은 /environment 디렉토리를 생성하고, 아래에 environment.yml과 데이터를 로드하는 environment-handler.ts를 정의하여 사용한다.

/environment/environment.yml

{% gist J911/b83e3245aa50386b6500e6c97da5cca9/4dcb8e0a79fdfe9dc99bb0b6a593c9df1174ee0a %}

/environment/environment-handler.ts

{% gist J911/50a485b222b3221094be1e821e274c19/097cc834f97cf8980a12673d34690f03d67601d3 %}