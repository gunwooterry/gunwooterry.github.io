---
layout: post
title:  Jekyll와 Travis CI로 블로그 세팅하기
date:   2017-06-24 15:10:00 +0900
tags:   cs
subclass:   'post'
categories: 'gunwooterry'
navigation: True
cover:  'assets/images/jekyll.png'
---
<br>
드디어 Jekyll과 Jasper 테마를 이용한 블로그 세팅을 마무리했다.
만들어진 테마를 살짝 커스터마이징하고 개발 환경을 구축하는 정도의 일인데도 꼬박 한나절이 걸렸다.
여러 사람들의 블로그를 참고하면서 배웠으니, 나도 고생하면서 배운 것들을 정리해 놓을까 한다.

### Jekyll?
Jekyll은 (지킬과 하이드의 그 지킬, 맞다) 사용자가 보다 쉽게 정적인 페이지를 만들 수 있도록 도와주는 Ruby 기반의 build tool이다.
지금 나는 마크다운 문법으로 이 글을 쓰고 있는데, 이걸 `<h4>Jekyll?</h4>`처럼 쓰려면 훨씬 귀찮겠지.
블로깅이라는 비교적 단순한 활동을 위해, 각 페이지마다 HTML 파일을 직접 만들고 있는 건 2017년에 어울리지 않는다.

사용자가 저장한 마크다운 파일, 이미지들을 잘 렌더링해서 HTML 파일로 만들어 주는 게 바로 Jekyll의 역할이다.
Github Pages의 기본 렌더링 엔진이기도 하고, 덕분에 좋은 테마들이 많이 있으니, 찾아보고 마음에 드는 걸 고르면 되시겠다.

### Jekyll 세팅하기
Jekyll 세팅 자체는 어렵지 않다. Jekyll 튜토리얼도 잘 나와있고, 한국어 자료가 필요하면 [좋은 블로그](https://zeniuus.github.io/posts/blog-with-github-pages-and-jekyll-get-started/)들도 많다.
특히 만들어진 테마를 다운받았다면 이미 완벽히 세팅이 되어 있는 경우가 많아서, 설명된 대로만 따라가면 큰 무리는 없을 것이다.

### 귀찮음 해결하기
오늘 이 글에서 중점적으로 다루고 싶은 건 Jekyll보다는 Travis CI를 이용해 두 repository를 연동시키는 방법이다.
Jekyll은 **정적이고 단순한** 페이지를 목표로 하기 때문에, Jekyll 자체로는 이 블로그에서 제공하는 태그 별로 모아보기조차 불가능하다.
이걸 해결하기 위해서, 내가 직접 수정하는 [develop repo](https://github.com/gunwooterry/blog)와
여기서 렌더링된 파일들이 저장되는 [output repo](https://github.com/gunwooterry/gunwooterry.github.io)를 만들었다.
Develop repo에서 글을 쓰고 build를 하면, output repo에 자동으로 저장되는 식이다.

여기서 첫 번째 문제 발생. Output 파일은 develop repo 내부의 폴더에 저장되는데, 두 repo는 독립적으로 관리되어야 한다.
이를 해결해 주는 것이 Git의 submodule이다. 두 repository를 별개로 관리하면서도, 하나가 나머지 하나를 포함하는 것이다.
나의 경우에 대입해 보면, develop repo 안에서 `git submodule add https://github.com/gunwooterry/gunwooterry.github.io.git _site` 명령으로
`_site`라는 폴더를 추가해주면 된다. 이제 이 폴더에 렌더링된 output 파일들이 저장되도록 설정하면 된다.

여기서 두 번째 문제 발생. 글을 쓰고 build하고 커밋하고 푸시하고, 다시 렌더링된 파일을 `git add`하고 커밋하고 푸시하고, 이 과정이 너무 귀찮다.
중간에 한 과정을 빼먹거나 커밋이 꼬이기도 쉽다. 이걸 자동으로 해 주는 게 바로 **Travis CI** 되시겠다.

### Travis CI
Travis CI는 오픈소스를 위한 CI 서버이다. CI 서버라 함은, 지속적으로 코드를 검사하고 테스트해 볼 수 있는 서버이다.
개발자가 직접 다양한 환경에서 코드를 테스트할 필요 없이, 코드를 서버에 푸시하면 설정에 따라 테스트를 진행해 주는 것이다.

나는 위에서 말한 두 repository를 동기화하는 과정을 Travis CI로 자동화했다. Travis는 내가 Github에 코드를 푸시할 때 trigger되는데,
이를 이용해 내가 develop repo에서 푸시할 때 자동으로 build해서 output 파일을 생성한 뒤 output repo에 커밋하고 푸시하도록 만든 것이다.
설정 파일은 [.travis.yml](https://github.com/gunwooterry/blog/blob/master/.travis.yml)처럼 만들면 된다.
Secure key 생성 등 커스터마이즈 과정은 파일에 주석으로 잘 설명되어 있다.

블로그 하나 만드려고 생각보다 많은 일을 벌였다. 뭐 결국에 다 배우자고 하는 짓이니...
귀찮은 일은 내가 하는 게 아니라 컴퓨터를 시켜야 한다는 걸 다시금 느끼게 되었다.
