---
layout: post
comments: true
title:  "지킬 블로그에 무한스크롤 기능 추가하기"
date:   2018-09-28
author: "J911"
categories: web
cover: "/assets/post-assets/2018-09-28-setup-jekyll-infinite-scroll/cover.png"
---
지킬 블로그에 포스팅이 점점 많아지면서 무한스크롤 기능이 필요하게 되었다.
특히 이 블로그처럼 카드 형태로 포스팅이 되는 디자인은 더더욱 무한스크롤이 보기가 좋다.

흠..하지만 적당한 MIT 라이센스의 무한스크롤 라이브러리를 찾을 수 없었다. 그래서 직접 만들었고, 여기서 그 라이브러리로 무한스크롤 기능 설치법을 소개할 것이다.

## 지킬에서 페이지네이션 사용하기
우선 지킬 페이지네이션 플러그인인 `jekyll-paginate`를 설치해주자.

_config.yml과 Gemfile 두 개를 수정해주면 된다.

_config.yml

```
paginate: 6 # 한 페이지에 보이게 할 피드의 개수
paginate_path: "/page/:num"

plugins:
    - jekyll-paginate
```

Gemfile
```
group :jekyll_plugins do
  gem "jekyll-paginate"
end
```

그리고 화면에 포스트들을 뿌려주는 html파일을 수정한다.
나와 같은 경우는 _layout/home.html을 수정해주면 된다.

_layout/home.html

{% gist J911/a430f0bd7efc15c972fa7e9a335e941b/88683326cd46db682f5c7c8f66d66f855971faae %}

이랬던 코드를

{% gist J911/a430f0bd7efc15c972fa7e9a335e941b/b1b594b8e4a3d762c982548cbb7d4aa7eeab8e11 %}

이렇게 바꾸었다. **site를 paginator**로 바꾼 것이 모두이다.

이렇게 따라할 경우 _config.yml에 설정한 경로로 설정 개수만큼 나누어 화면에 뿌려줄 수 가있다.

이 글을 따라오셨다면 /page/2, /page/3 에서 다음 피드들을 나누어 볼 수 있다.

## 본격 무한 스크롤 만들기
이제 무한스크롤 라이브러리를 다운 받아야한다.

[https://github.com/J911/jekyll-infinite-scroll/archive/master.zip](https://github.com/J911/jekyll-infinite-scroll/archive/master.zip)링크로 모듈을 다운받거나

```
$ bower install jekyll-infinite-scroll --save
```

과 같이 bower을 통해 설치를 하자.

다음과 같은 CDN을 사용해도 된다. 하지만 모듈을 다운받아 사용하는 것을 권장한다.

```
<script src="https://cdn.rawgit.com/J911/jekyll-infinite-scroll/master/lib/InfiniteScroll.js"></script>
```

나는 모듈을 다운받아 /assets/lib/InfiniteScroll.js 위치로 옮겨놓았다.

그리고 이제 아까 수정했던 home.html을 다시 수정할 것이다.

{% raw %}
```
<script src="{{ '/assets/lib/InfiniteScroll.js' }}"></script>
```
{% endraw %}
다음과 같이 라이브러리를 불러와주자.

이 이후 게시글 전체를 감싸고 있는 태그의 id값과 _config.yml에 입력한 paginate_path값에서 :num 이전 까지의 주소를 변수에 저장해주자.

```
var postWrapperId = 'card-wrapper';
var paginatePath = '/page/'
```

여기서 /로 끝나는것을 잘 보아야 한다. 그렇지 않으면 /page/:num이 아닌 /page:num 으로 접근하는 것이다. 

그리고 그것을 `jekyll-infinite-scroll` 객체로 만들어주면 끝이 난다.

```
new InfiniteScroll(paginatePath, postWrapperId);
```

완성된 코드는 다음과 같다.

{% gist J911/a430f0bd7efc15c972fa7e9a335e941b/f9fb865d0b25d84f9811cf4508663ee1fe85c646 %}

## 결과물
![result](/assets/post-assets/2018-09-28-setup-jekyll-infinite-scroll/infinite-scroll.gif)

마지막으로 위에서 사용된 jekyll-infinite-scroll 모듈의 깃허브 저장소는 [https://github.com/J911/jekyll-infinite-scroll](https://github.com/J911/jekyll-infinite-scroll.git)이다.