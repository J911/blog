---
layout: post
comments: true
title:  "지킬 블로그 구축하기"
date:   2018-09-22
author: "J911"
categories: web
cover: "/assets/post-assets/2018-09-22-create-jekyll-blog/cover.jpg"
---
오랜만에 블로깅을 다시 시작하고자 블로그를 리뉴얼 하였다.

처음 사용한 블로그 플랫폼은 브런치였는데, *브런치*는 마크다운으로 글을 쓸 수가 업고 검색엔진에서 노출되기가 힘들다는 문제가 있었다.
그래서 그다음으로 시도했던 플랫폼은 *github page*에 *ghost*를 바인딩 하여 사용했다.
이 또한 처음에는 괜찮았는데 범용 테마를 선택해 쓰다 보니 개성이 없어 보이고 흥미가 사라져 갔다.

그래서 이참에 **jekyll**로 테마를 만들어 나만의 개성 있는 블로그를 만들고자 하였다.

그래서 다음과 같은 명세를 만들고 웹페이지를 만들었다.

1. 나의 개성을 잘 보일 수 있는 테마여야 한다.
2. 블로그는 최대한 깔끔하여야 한다.

그리고 그 웹페이지를 jekyll로 적용하기 위해 개발을 시작했다.

## 개발하려니 생긴 문제
개발을 시작하려고 보니 문제가 하나 생겼다.

나는 jekyll에 대해 전혀 모른다는 것이었다.

그래서 [jekyll 문서](https://jekyllrb-ko.github.io/)를 보고 따라해보면서 익히기 시작했다.

## jekyll 설치하기
우선 jekyll은 ruby로 개발되어있어 gem으로 jekyll을 설치한다.
```
$ gem install bundler jekyll
```
설치가 완료되면 다음과 같이 jekyll 프로젝트를 만들수 있다.
```
$ jekyll new my-awesome-site
$ cd my-awesome-site
```
등록이 완료되면 다음 명령으로 localhost:4000에서 확인할 수 있다.
```
$ bundle exec jekyll serve
```

## jekyll 프로젝트 분석하기
my-awesome-site의 목록을 열어보면 다음과 같은 구조가 자동 생성되어있다.
```
.
├── 404.html
├── Gemfile
├── _config.yml
├── _posts
│   └── 2018-09-22-welcome-to-jekyll.markdown
├── about.md
└── index.md
```

- _config.yml: jekyll 블로그의 메타데이터를 기록하고있다.
- 404.html: 페이지를 찾을 수 없을 때 랜더링되는 페이지.
- Gemfile: jekyll에 등록할 플로그인등을 등록할 수 있는 문서.
- _posts: 블로그의 포스트를 기록하는 디렉토리
- about.md: /about.html로 연결될 정적페이지
- index.md: /index.html로 연결될 정적페이지 

우선 나에게 about.md파일은 필요가 없는 페이지였으로 삭제하였다.   
그리고 블로그를 커스텀 하기 위해 필요한 디렉토리를 몇 개 더 만들어야 한다.

- _layout: 블로그의 레이아웃 템플릿을 저장할 디렉토리
- _include: 레이아웃에서 불러 사용할 수 있는 html조각을 저장할 디렉토리
- assets: 블로그에서 다룰 정적 리소스를 저장할 디렉토리

## 본격 커스텀 시작하기
나는 default 레이아웃을 등록해 기본 레이아웃을 잡았다.

{% gist J911/2913c819bed9d1527c74e924ac630e13 %} 


다음과 같이 `default.html`을 만들고 default에서 인클루드한 html조각들을 `_include`에 등록해 주었다.

```
_includes
├── analytics.html
├── footer.html
├── head.html
├── loader.html
├── navigator.html
└── side-menu.html
```

그리고 레이아웃에서 사용할 CSS파일을 `/assets/css`에 등록하고 `heade.html`에서 불러와 사용했다.

```
assets
└── css
    └── main.css
```

{% gist J911/265f09fe39bf90e8a16dd1a79fe81fe3 %}

## 포스팅하기
이후 여러 레이아웃을 추가 한 뒤 첫 글을 게시해보았다.

포스팅은 `_post` 디렉토리에 마크다운으로 작성을 하면 되고 다음과 같은 규칙을 문서 상단에 작성해야한다.

```
---
layout: post   
title:  "지킬 블로그 구축하기"   
date:   2018-09-22   
author: "J911"   
categories: log jekyll   
---
```
- layout: 문서를 보여줄때 사용할 레이아웃
- title: 문서의 제목
- date: 문서의 날짜
- ...

이외에도 해당 문서의 추가적인 메타데이터가 있다면 등록하여 사용할 수 있다.

## 마치며
간단하게 내용을 정리해 보았는데, 사실 많이 부족한 문서이다.
jekyll은 생각보다 간단하면서도 복잡하게 이루어져있다.
이에 대하서는 다음 기회에 상세하게 작성해 기록해야겠다.


