---
layout: post
comments: true
title:  "Bower로 나의 컴포넌트 배포하기"
date:   2018-10-03
author: "J911"
categories: web
cover: "/assets/post-assets/2018-10-03-publish-bower-component/cover.png"
---
Bower에 컴포넌트를 배포하는 것 npm에 모듈을 등록하는 것과 비교해보면 정말 간단하다.

## 우선 Bower는 무엇일까.
**bower**는 **프론트엔트 패키지 관리도구**라고 한다. 이름만으로 직관적으로 해석해 보았을 때 프론트엔드 개발에 필요한 컴포넌트를 설치하고 관리하는 모듈로 해석이 된다. 그리고 그 것이 맞다.

다음과 같은 명령행을 통해 자신의 프로젝트에 컴포넌트를 설치할 수 있다.
```
bower install <package-name> --save
```

## Bower에 직접 컴포넌트 배포하기
Bower는 자체 저장소를 제공하지 않고 git 저장소를 기반으로 컴포넌트 관리를 제공한다.

때문에 bower에 컴포넌트를 등록하고자 한다면 사전에 git 저장소를 가지고 있어야 한다는 뜻 이다.

준비가 다 되었다면, `bower init` 이라는 명령으로 본인에 패키지에 bower.json 파일을 생성해주자. 만약 package.json파일이 있다고하면 거의 모든 내용은 자동으로 만들어질 것이다.

```
$ bower init
```

그러면 본인 패키지에 bower.json이 생성이 될 것이다. 이를 열어서 ignore에 배포하지 않을 파일 및 디렉토리를 등록하자.

모든 것이 완료 됬으면, `bower register` 명령으로 패키지를 등록할 수 있다.

```
$ bower register <component-name> <git>
```

명령의 첫 번째 인자로는 컴포넌트 이름을 작성하고 두 번째 인자는 깃 저장소의 주소를 넣어주면 된다.

