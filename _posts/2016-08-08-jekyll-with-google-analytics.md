---
layout:   post
title:    "Jekyll에 Google Analytics 추가하기"
date:     2016-08-08 12:42:08 +0900
tags:     [jekyll, google analytics]
comments: true
---

Google Analytics 는 웹페이지 방문자들의 사이트 활동, 사이트 유입경로 등 웹로그를 분석해주는 서비스이다. 블로그에 이 기능을 추가해 보자.

## Prerequisite

먼저 [Google Analytics](https://www.google.com/analytics/)에 로그인하여, 추적 ID를 생성한다.

## Usage

html 파일의 `<head>` 태그 안에 아래 스크립트를 추가한다.

```html
<!-- Google Analytics -->
{% if site.google-analytics %}
<script>
(function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
})(window,document,'script','https://www.google-analytics.com/analytics.js','ga');

ga('create', '{{ site.google-analytics.id }}', 'auto');
ga('send', 'pageview');

</script>
{% endif %}
```

그리고 `_config.yml` 파일에 Google Analytics 에 대한 정보를 추가한다. `YOUR_GOOGLE_ANALYTICS_ID`에 위에서 생성한 추적 ID를 입력한다.

```yml
google-analytics:
  id: "YOUR_GOOGLE_ANALYTICS_ID"

```
