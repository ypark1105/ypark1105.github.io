---
layout:   post
title:    "Jekyll에서 Pagination을 사용 할 때의 문제점"
description: ""
date:     2016-07-19 03:02:12 +0900
tags:     [jekyll]
comments: true
share: true
---

Jekyll의 paginator 기능이 root에 있는 index.html 에서만 적용되는 문제가 있었다.

```
for post in paginator.posts
```
즉, 위 코드가 `/root/index.html `에서만 동작했는데, 나는 `/root/blog/index.html` 에 이 코드를 적용시키고 싶었다.

한참을 삽질과 검색으로 시간을 보낸 결과 방법을 찾았다.
아주 간단하게도, _config.yml 파일에서 paginate_path 부분이
`paginate_path: "page:num"`으로 되어있었다면`paginate_path: "blog/page:num" `
으로 변경하면 되는 것이었다.

알게된 것을 덧붙이자면, Jekyll의 Pagination 기능은 index.html 이라는 이름의 파일에서만 동작해서, blog.html, about.html 같은 이름의 파일에는 적용이 되지 않는다고 한다. 따라서 blog.html 파일의 코드에 Pagination 을 적용하고 싶다면 반드시 blog라는 폴더를 만들고 그 안에 index.html 로 이름을 바꿔서 넣어야 한다. _config.yml 파일은 수정한 다음 jekyll을 재시작해야 변경사항이 적용된다는 것도 잊지 말자.
