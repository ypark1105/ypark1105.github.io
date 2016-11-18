---
layout: post
title: "Django and Bower"
description: "Django의 프론트엔드에서도 Django-bower를 사용해서 편리하게 패키지를 관리할 수 있다."
date: 2016-11-19
tags: [django, bower]
comments: true
share: true
---

# Django의 프론트엔드 의존성 관리 툴 Django-bower

Angular JS, React JS 에서 package.json을 사용하는 것처럼 django의 프론트엔드에서도 [Django-bower](https://django-bower.readthedocs.io/en/latest/)를 사용해서 편리하게 패키지를 관리할 수 있다.

{% raw %}

# Installation

먼저 bower를 전역으로 설치해야 한다.

```bash
$ npm install -g bower
```

그리고 pip를 통해 django-bower를 vitrualenv에 설치한다.

```bash
(env) $ pip install django-bower
```

다음으로 `settings.py`에

1. `INSTALLED_APPS` 에 `djangobower`를 추가한다.
2. `STATICFILES_FINDERS`에 `djangobower.finders.BowerFinder`를 추가한다.
3. `STATIC_ROOT`를 추가한다. 이 때, 경로는 `STATICFILES_DIRS`의 경로와 다른 경로여야 한다.
4. 패키지가 설치된 경로인 `BOWER_COMPONENTS_ROOT`를 추가한다.
5. 설치할 패키지의 목록인 `BOWER_INSTALLED_APPS`를 추가한다.
#### setting.py 예시

```python
INSTALLED_APPS = [
    ...
    'django.contrib.staticfiles',
    'djangobower'
    ...
]

STATIC_ROOT = os.path.join(BASE_DIR, 'static_root')

STATIC_URL = '/static/'

STATICFILES_DIRS = [
    os.path.join(BASE_DIR,'static'),
]

STATICFILES_FINDERS = [
    'django.contrib.staticfiles.finders.FileSystemFinder',
    'django.contrib.staticfiles.finders.AppDirectoriesFinder',
    'djangobower.finders.BowerFinder',
]

BOWER_COMPONENTS_ROOT = os.path.join(BASE_DIR, 'components')

BOWER_INSTALLED_APPS = (
    'jquery#1.9',
    'underscore',
)
```

# Usage

다음의 명령으로 `BOWER_INSTALLED_APPS`에 나열된 패키지들을 설치할 수 있다. 그러면 `BOWER_COMPONENTS_ROOT`에 지정해 둔 경로에 패키지들이 설치된다. 위의 예시에서는 `components` 폴더에 설치된다.

```bash
(env) $ python manage.py bower install
```

그리고 다음 명령으로 `STATIC_ROOT` 폴더를 생성한다. 이 명령은 `STATICFILES_FINDERS`를 통해 `STATICFILES_DIRS` 와 `BOWER_COMPONENTS_ROOT` 를 통합하여 `STATIC_ROOT` 를 생성해준다. 위의 예시에서는 `static` 폴더와 `components` 폴더를 통합한 `static_root` 폴더를 생성한다.

```bash
(env) $ python manage.py collectstatic
```

설치한 패키지를 템플릿에서 사용하려면 다음과 같이 쓰면 된다. 꼭 `{% load staticfiles %}`를 먼저 써주어야 한다!

```html
{% load staticfiles %}
<script type="text/javascript" src='{% static 'jquery/jquery.js' %}'></script>
```

추가로, `pip freeze`와 같이 `bower freeze` 기능도 제공한다.

``` bash
(env) $ python manage.py bower freeze
```

{% endraw %}