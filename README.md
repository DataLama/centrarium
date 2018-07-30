jekyll blog 정리
===============

---

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

또한 열심히 구글링하다 보면 위의 스크립트를 `_layout` 폴더 내의 `post.html`에서 `<article>`태그 안에 사용하라고 한다. 요런 느낌으로 쓰라고 하는 듯??

```html
<article class="post-content">
  <script type="text/x-mathjax-config">
	MathJax.Hub.Config({tex2jax: {inlineMath: [['$','$'], ['\\(','\\)']]}});
  </script>
  <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
  {{ content }}
</article>
```
