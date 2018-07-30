jekyll blog 정리
===============
본 블로그는 [Ben Centra](https://github.com/bencentra)의 Centrarium 테마를 바탕으로 만들었습니다. `README.md` 파일은 간단한 기능들에 대한 설명만 정리했습니다. 보다 자세한 설명은 [여기](https://github.com/bencentra/centrarium)를 참고하시기 바랍니다.

---

Configuration
---------------

### 포스팅 기본 설정

* `_posts` directory에 마크다운 파일을 추가하면됨.
* 파일명은 `YYYY-MM-DD-name-of-post.markdown`으로 만들어주기.
* 포스팅할 파일은 다음의 사항을 반드시 써주자.

<br>

``` 
---
layout: post                   # 해당 마크다운이 post라는 것을 나타냄.
title:  "네트워크 이론의 기초"   # 해당 포스트의 제목.
date:   2018-07-30 08:43:59    # 생성일자 타임스템프 (귀찮으면 그냥 일자만 쓰자)
author: datalama               # 작성자
categories: Python             # 카테고리 할당.(`_config.yml`에 설정된..)
custom_js :                    # 해당 포스트에 적용하는 커스터마이징한 js (`js`폴더에 있는 js이름 넣기)
tags: tag1 tag2                # 태그... 띄어쓰기를 통해 사용
cover: "assets/cover_image.jpg"# 커버 이미지 등록 후.
---
```

### 마크다운 정리

* 기본적으로 kramdown 문법 참고하자 [요기](http://gjchoi.github.io/env/Kramdown(%EB%A7%88%ED%81%AC%EB%8B%A4%EC%9A%B4)-%EC%82%AC%EC%9A%A9%EB%B2%95/).

* syntax highlighting settings  
`{% highlight <language> %}`을 통한 코드 블럭 생성 가능

```
{% highlight javascript %}
function demo(string, times) {
  for (var i = 0; i < times; i++) {
    console.log(string);
  }
}
demo("hello, world!", 10);
{% endhighlight %}
```

* kramdown 문서를 어떻게 만들지??...

### 카테고리 추가

`_config.yml` 파일에서 아래의 `cat`과 `desc`를 추가하는 방법을 통해 새로운 카테고리를 구성할 수 있음.

```
# Category descriptions (for archive pages)
descriptions:
  - cat: Python
    desc: "Reviews of all about python programming"

  - cat: Network Analysis 
    desc: "Reviews of all about Network Analysis"
```



Issues
---------------

### MathJax 연동.

우여곡절 끝에 블로그를 만들고 첫 포스팅을 할 때, latex 문법이 적용되지 않는 것을 보고 당황을 했다. 한숨을 쉬면서 검색을 해본 결과 다음의 사실을 알 수 있었다.

* github의 기본 마크다운 실행기(?)인 kramdown은 latex를 기본으로 지원하지 않는다.
* github은 원래 다양한 마크다운을 지원했지만 2016년 이후로 github의 공식 마크다운은 kramdown이다....
* kramdown은 ruby 기반의 마크다운이다......

> (개인적으로 지금껏 데이터 분석이나 개발을 하면서 맥에 대한 필요성을 느끼지 못했었는데... 이번에 jekyll기반의 블로그를 만들면서 개발 세계에서 윈도우는 거의 6두품 취급이라는걸 뼈저리게 느꼈다...후...)
<br>
그럼에도 잘 찾아보니... 다음의 MathJax 스크립트를 넣으면 된다는 사실을 알게 되었다.
<br>

```html
<script type="text/x-mathjax-config">
	MathJax.Hub.Config({tex2jax: {inlineMath: [['$','$'], ['\\(','\\)']]}});
</script>
<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
```
<br>
여기서 중요(?)한거는 __두 번째 스크립트의 src에서 `https`로 해줘야지__ 잘 작동이 된다는 점이다.(`http`로 하면 latex가 적용이 안되는 것은 아니지만...  “안전하지 않은 스크립트 로드”를 하라는 alert창이 뜬다는 문제가 있다. 물론... 프론트엔드 개발자에게는 당연한거일 수도 있지만 일개 데이터 분석쟁이 한테는 어려운 ㄷ...)

<br>
<br>
또한 열심히 구글링하다 보면 위의 스크립트를 `_layout` 폴더 내의 `post.html`에서 `<article>`태그 안에 사용하라고 한다. 요런 느낌으로 쓰라고 하는 듯??
<br>

```html
<article class="post-content">
  <script type="text/x-mathjax-config">
	MathJax.Hub.Config({tex2jax: {inlineMath: [['$','$'], ['\\(','\\)']]}});
  </script>
  <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
  {{ content }}
</article>
```
만약 `post.html`에서만 latex가 렌더링 되길 바란다면 상관 없지만... 그래도 혹시 모르니까 `_includes`의 `footer.html`에 스크립트를 달아두었다.ㅎㅎ

### disqus(댓글 기능) 추가

진행중입니다.

### 한글 폰트 적용.

진행중입니다.

### 태그 관리

진행중입니다.

### 기타...
잉여력이 갖춰지면 할 일들.
* 아이콘, 파비콘 만들기
* GA 추가
