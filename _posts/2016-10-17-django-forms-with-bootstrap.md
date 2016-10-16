---
layout: post
title: "Django Form에 Bootstrap 적용하기"
description: ""
date: 2016-10-17
tags:
  - django
  - bootstrap
comments: true
share: true
---

> 라고 쓰고, Django에 Bootstrap을 적용하던 중 겪었던 **삽질기** 라고 읽는다.

### Problem

```html
<form class="form-horizontal">
  <div class="form-group">
    <label for="inputUrl" class="col-sm-2 control-label">Video Url</label>
    <div class="col-sm-10">
      <input type="text" class="form-control" id="inputUrl" placeholder="Input Url here">
    </div>
  </div>
  <div class="form-group">
    <div class="col-sm-offset-2 col-sm-10">
      <button type="submit" class="btn btn-default">Search</button>
    </div>
  </div>
</form>
```

이와 같이 각종 태그를 적용시키고 싶은데,

{% raw %}
```html
<form method="POST">
  {% csrf_token %}
  {{ form.as_p }}
  <button type="submit">Search</button>
</form>
```
{% endraw %}

Django에선 위 코드의 `form.as_p`처럼 뷰에서 넘겨받은 form 객체를 렌더링 시키기 때문에 태그를 적용할 수가 없었다..!

그래서 한참을 구글링과 삽질을 반복한 결과..

### Solved

```python
from django import forms

class UrlSearchForm(forms.Form):
    search_url = forms.CharField(
            label="Video Url",
            widget=forms.TextInput(
                attrs={
                    "class": "form-control",
                    "id": "inputUrl",
                    "placeholder": "Input Url here",
                })
            )
```

와 같이 간단히 `form field`에 `widget`을 추가하여 해결할 수 있었다.

#### 참고 링크

[Django Documentation - Working with forms](https://docs.djangoproject.com/en/1.10/topics/forms/)
