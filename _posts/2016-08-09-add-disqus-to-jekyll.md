---
layout:   post
title:    "Jekyll에 Disqus 추가하기"
date:     2016-08-09 19:38:00 +0900
tags:     [jekyll, disqus]
comments: true
---

Jekyll에 Disqus를 추가하는 방법을 알아본다. Disqus는 정적 웹사이트에서 동적 기능인 댓글을 사용할 수 있는 좋은 방법이다.

## Prerequisite

[Disqus](https://disqus.com)에 로그인하여 새로운 Disqus site를 생성한다. 이때 `shortname.disqus.com`라고 생성하면, `shortname` 이 아래에서 사용할 `Disqus ID`가 된다.

## Install

Jekyll 코드에 Disqus 코드를 추가한다. `{% if page.comments %}` 태그와 `{% endif %}` 태그 사이에 [Universal Embed Code](https://disqus.com/admin/universalcode/)을 넣은 스크립트를 원하는 곳에 추가한다. 나는 `_config.yml`에 전역변수로 Disqus ID를 지정하고 다음 코드를 사용하였다.

```
{% if page.comments %}
<div id="disqus_thread"></div>
<script>

(function() { // DON'T EDIT BELOW THIS LINE
var d = document, s = d.createElement('script');
s.src = '//{{ site.disqus.id }}.disqus.com/embed.js';
s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
{% endif %}

```

```yml
disqus:
  id: "YOUR_DISQUS_ID"
```


Disqus를 사용하려면, YAML frontmatter에 아래와 같이 `comments: true`를 삽입하면 된다. `comments: false`나 comments: 자체를 넣지 않으면 Disqus가 나타나기 않는다.

```md
---
layout: default
comments: true
# other options
---
```
