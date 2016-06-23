---
layout: post
title: "MDN 페이지 수정"
date: 2016-06-24 00:04:00
---

MDN 자바스크립트 [표현과 연산자](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Expressions_and_Operators#Arithmetic_operators)를 읽다가 한국어 페이지 링크가 깨진 것을 발견했다.   
a 태그가 가리키는 id부분이 잘못된 아주 간단한 문제.

#### AS-IS
{% highlight javascript %}
web.link("#Assignment_operators", "대입 연산자")
{% endhighlight %}

#### TO-BE
{% highlight javascript %}
web.link("#대입_연산자", "대입 연산자")
{% endhighlight %}

** [리비전 비교](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Expressions_and_Operators%24compare?from=994001&to=1074594)

반영하면 아래 같은 안내가 나온다.
<img src="/assets/mozilla_update.png"/>
PR을 받아주길 기다리는 시간은 아닌 것 같고, 아마 CDN같은 리소스를 업데이트 하고 있지 않을까?   
