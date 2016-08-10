---
layout:   post
title:    "여러 개의 github 계정 사용하기"
date:     2016-07-16 17:34:43 +0900
category: TIL
tags:     [github, ssh]
---

새 github 계정이 필요해져서 사이트에서 계정을 만든 뒤 로컬에서 작업을 하고 푸시하려는데 권한이 없다는 오류가 났다. 그래서 한 대의 컴퓨터에서 여러 개의 github 계정을 사용하는 방법을 찾아서 정리해보았다. github 사이트에서 한 계정을 사용중이다가 새로이 두 번째 계정을 만드는 것을 설명한다.

### 1. 새로운 SSH 키 만들기
SSH 키들은 기본적으로 사용자의 ~/.ssh 디렉토리에 저장된다. 먼저 기존의 키들을 확인하자.

```bash
 $ cd ~/.ssh
 $ ls
 id_rsa.pub    id_rsa    known_hosts
```

.pub 이 붙은 파일과 그렇지 않은 파일을 볼 수 있는데, .pub 이 붙은 것이 공개키이고 다른 것은 개인키이다.

자 이제 다음의 명령으로 새로운 SSH 키를 만들어 보자. 기존에 생성된 SSH 키가 없거나, .ssh 디렉토리가 없어도 다음 명령으로 만들 수 있다.

```bash
 $ ssh-keygen -t rsa -C "username@gmail.com" // 새 계정의 이메일 주소
```

그럼 먼저 새로운 키를 저장할 경로를 묻는데 이 때, **기존의 키를 덮어쓰지 않도록 조심하자.** 여기서는 ```id_rsa_second``` 라는 이름으로 SSH 키를 생성한다.

```bash
 $ Enter file in which to save the key (/Users/YOURNAME/.ssh/id_rsa):/Users/YOURNAME/.ssh/id_rsa_second
```

다음으로 암호를 두 번 입력하라고 하는데 엔터를 쳐서 넘어가자. 그러면 새로운 키가 생성된 것을 확인할 수 있다.

```bash
 $ ls
 id_rsa.pub    id_rsa_second.pub   id_rsa    id_rsa_second   known_hosts
 $ cat id_rsa_second.pub
  ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDBvlgEqRqaVp/zOoxtAnKdMr6/y9SaP0Y3MGG4648N+MLD6yy+JjOYE3HnLNDWsOhsOXkjr7phVHYBqVd6QtpHZrgw5PXOEo1V00Es+HGcHU0sONLWK/OWtV7598eULXnQfNjPlND/09BW+D5IXI8plNRcjfaD4dRxtSOtolZ5jxxxT4gpR5v17Axm3ut4ukS+6f6GHNYZ4QcZJtlaps+eN0Ol/juEYy47r3l5CPIc9sxyQGE4o5Mm4LhLk769yVQGgGcR21Aj0DuEVN0HyeEZcAbqFqze9ZY5kdtYcI2L4B23X781nlX6zfpeVL9iU9pxkw/UGLUx2bcSGHOfrvhX username@gmail.com
```

`id_rsa_second.pub` 의 내용을 복사해서 다음 단계에서 사용할 것이다.

### 2. SSH 키 설정하기

[github](https://github.com) 사이트에서 두 번째 계정을 생성하고 로그인 한 다음, 오른쪽 위의 아이콘을 클릭하여 Settings ->  SSH keys -> New SSH key 를 클릭한다.
다음과 같이 Title 입력란에는 구별할 수 있는 간단한 이름을, Key 입력란에는 복사해둔 키를 붙여 넣고, Add SSH key 버튼을 눌러 완료한다.

![new SSH key](/assets/images/blog/2016-07-16-1.png)

그 다음 다시 터미널로 돌아와서 생성한 키를 SSH에 추가해 주어야 한다.

```bash
  $ ssh-add ~/.ssh/id_rsa_second
```

### 3. Config 파일 만들기
이제 로컬에서 작업한 것들을 github로 푸시할 때 어떤 키를 참조할 것인지 결정할 수 있도록 config 파일을 만들자.

```bash
 $ cd .ssh
 $ vim config
```

config 파일에는 다음의 내용을 입력한다.

```bash
  # Default account
  Host github.com
      HostName github.com
      User git
      IdentityFile ~/.ssh/id_rsa
  # Second account
  Host github.com-second
      HostName github.com
      User git
      IdentityFile ~/.ssh/id_rsa_second
```


### 4. 새 계정으로 Push하기

지금까지의 작업이 잘 되었는지 확인해보자. 먼저 github 사이트에서 새 repository 를 만든다. 그리고 로컬에서 원하는 위치에 새 폴더를 만들고 다음 명령을 입력한다.

```bash
  $ git init
  $ git commit -am "first commit"
  $ git remote add origin git@github.com-second:YOURNAME/REPOSITORY.git
  $ git push origin master
```

여기서 `YOURNAME` 에는 github 계정의 이름, `REPOSITORY` 에는 새로 만든 repository의 이름을 입력한다. 유의해야 할 점은, config 파일에서 `Host github.com-second` 라고 입력했으므로 원격저장소 설정시 `git@github.com` 대신 `git@github.com-second` 를 입력해야 한다는 점이다. 이것은 clone 시에도 마찬가지이다. 만약 기존의 계정으로 작업하려면 원래하던 방법으로 `git@github.com`으로 하면 된다.


추가적으로, 새 계정으로 작업하는 폴더에서 다음 명령으로 commit 시 사용될 이름과 이메일 주소를 변경할 수 있다.

```bash
  $ git config user.name "YOURNAME"
  $ git config user.email "YOUREMAIL"
```




_NOTE_ - 만약 잘 사용하다가 갑자기 git access denied 라는 메세지가 뜨면 아래 명령을 입력한 뒤 다시 시도해보라.

```bash
  $ ssh-add ~/.ssh/id_rsa_second
```


---

#### 참고 링크
  * [How to Work with GitHub and Multiple Accounts](http://code.tutsplus.com/tutorials/quick-tip-how-to-work-with-github-and-multiple-accounts--net-22574)
  * [Git 서버 SSH 공개키 만들기](https://git-scm.com/book/ko/v1/Git-서버-SSH-공개키-만들기)
