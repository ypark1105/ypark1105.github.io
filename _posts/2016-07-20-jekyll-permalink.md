---
layout:   post
title:    "Jekyll 블로그에서 포스트 URL 구성하기"
date:     2016-07-20 14:42:59 +0900
category: TIL
tags:     [jekyll]
---

Jekyll로 만들어진 블로그에서 URL을 정하는 방법은 _config.yml에서 블로그 전반적인 주소 형식을 정하는 방법과, YAML 머리말로 포스트마다 정해주는 방법 두가지가 있다.

### _config.yml 에서 설정하기

_config.yml 파일을 열어보면 `permalink: `으로 시작되는 부분이 있을 것이다. 만약 이 부분이 없다면 기본값인 `permalink: date` 형식을 따르게 되는데, 이는 `permalink: /:categories/:year/:month/:day/:title.html` 와 같다. `date` 값은 Jekyll 에서 편의를 위해 기본으로 제공되는 스타일 중 하나이다.

### YAML 머리말로 설정하기

{% highlight markdown %}
---
layout:
title:
date:
permalink: date
---
{% endhighlight %}

위와 같이 포스트의 YAML 머리말에 `permalink: `를 표시해 준다. 이렇게 하면 _config.yml 파일에서 정한 형식을 덮어쓰게 된다.


### 예시
  `permalink: /:short_year-:month-:day/:title.html` 와 같이 원하는 스타일로 지정할 수도 있다. 이렇게 하면 포스트 이름이 `/2016-07-20-jekyll-permalink.md` 인 경우 `/16-07-20/jekyll-permalink.html` 로 나타난다. 확장자 없이 `permalink: /:short_year-:month-:day/:title`도 가능하다.
`:category`, `:year`, `:title` 과 같은 변수와 예시가 공식 문서에 자세히 나와 있으니 참고하도록 하자.

---

#### 참고 링크
* [지킬 공식 사이트 - 고유주소](https://jekyllrb.com/docs/permalinks/)
* [지킬 공식 사이트(한글 번역) - 고유주소](http://jekyllrb-ko.github.io/docs/permalinks/)
