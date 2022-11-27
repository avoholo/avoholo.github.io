---
title: "Airflow Operators"
date: 2022-11-21 00:00:47+0900
modified: 2022-11-21 00:28:47+0900
tags: [Airflow]
description: Airflow Operators
image: "/_posts/Airflow_operators/default_post_image.png"
---

![default_post_image](https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Git_git_fundamental_walkthrough/default_post_image.png)



<br>

## How it works

<figure>
<img src="https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Git_git_fundamental_walkthrough/how_git_works.png" alt="how_git_works">
<figcaption>Fig 1. Airflow DAGs</figcaption>
</figure>


***

<br>

`Airflow`의 장점을 하나 꼽으라면 다양한 종류의 Operator를 지원한다는 것이다. 

*여기서 Operator는 뭘까?*  일단 *간단히* 말하자면 Airflow의 `Task`를 실행시켜준다고 생각하면된다.

**Airflow**에서 실행할 작업들을 순서대로 구성한 Workflow를 DAG<sup id="medium">[[1]](#medium-ref)</sup>라고 한다. 

`DAG`에서 수행되는 각 작업을 `Task`라고 하며, 이 **Task**에는 총 3가지의 종류가 있다:

- *Operator*
- *Sensor<sup id="medium">[[2]](#medium-ref)</sup>*
- *Hook*

이중 오늘 포스트에선 ***Operator*** 에 대해 다뤄볼 예정이다. 참고로, Operator도:



1) Action - 파일을 실행하는 operator 

2) Transfer - Source to Destination으로 data를 transfer 해주는 operator

로 나뉘게 되지만 더 자세한 내용은 아래에서 다룰것이다.

<br>

### 1. Initialize

#### Init

`git init`는 새로운 Git 저장소(repository)를 생성할 때 사용하는 Git 명령어 입니다.

~~~bash
git init
Initialized empty Git repository in /Users/dale/temp/our-project/.git/
~~~

<br>

#### .git의 초기구성

```bash
HEAD
config
description
/branches
/hooks
/objects
/refs
```

<br>

#### .gitignore 생성

예로, `.env` 파일은 많은 자바스크립트 프로젝트에서 개발자들이 로컬 컴퓨터에 임의의 환경 변수를 설정하기 위한 용도로 사용합니다. 따라서 이 파일은 `.gitignore` 파일에 등록을 해야 보안적으로 안전하고 개발자 간에 불필요한 코드 충돌을 피할 수 있습니다.

`.gitignore` 파일을 생성하고, 위에서 디렉토리를 생성할 때 함께 생성해놓은 `.env` 파일을 등록하겠습니다.

```bash
echo .env > .gitignore

cat .gitignore
.env
```

<br>


### 2. To Github (remote)



#### SSH

~~~bash
ssh-keygen -t rsa -C “avoholo@github.com” # or
ssh-keygen -m PEM -t rsa -C "avoholo@github.com" # PEM 설정은 골라서 사용하자.
cat ~/.ssh/id_rsa.pub
~~~

Settings > SSH and GPG Keys > New SSH key > add

~~~bash
$ vi ~/.ssh/config

Host github.com
    HostName github.com
    User git
    IdentityFile /c/Users/avoholo/.ssh/id_rsa
~~~

local에서 remote 접근시 `private key`를 사용하고, github에는 `public key`(id_rsa.pub)를 등록 해야합니다.

<br>

[공식 가이드](https://docs.github.com/en/authentication/troubleshooting-ssh/error-permission-denied-publickey#always-use-the-git-user)에는 `User` 항목에 `git` 을 쓰라고 되어있는데,  절반 이상의 개인 블로그나 StackOverflow에는 자신의 **github username**을 사용하라고 하네요.

이것 때문에 삽질을 30분 동안 했는데, 인터넷에는 **검증되지 않은 정보가 많은것 같습니다.**

<figure>
<img src="https://raw.githubusercontent.com/avoholo/avoholo.github.io/master/_posts/Git_git_fundamental_walkthrough/github_docs.png" alt="github_docs">
<figcaption>Fig 2. Github Official Docs</figcaption>
</figure>




아래 명령어로 **인증 테스트**를 할 수 있습니다.

~~~bash
ssh -vT github.com
~~~

`~/.ssh/config`에 등록 해놨던 설정대로 접속이 된걸 확인 할 수 있습니다.

~~~bash
$ ssh -vT git@github.com
Hi avoholo! You've successfully authenticated, but GitHub does not provide shell access.
debug1: channel 0: free: client-session, nchannels 1
Transferred: sent 3216, received 2704 bytes, in 0.4 seconds
Bytes per second: sent 7578.5, received 6371.9
debug1: Exit status 1
~~~

<br>

#### Remote Url

만약 url이 `https` url로 설정되어 있다면, 인증시 `username`과 `password`를 요구합니다. `ssh url`로 바꿉니다.

~~~bash
git remote set-url git@github.com:avoholo/avoholo.github.io.git
~~~

<br>

#### Status (항상 먼저 체크하자)

~~~ bash
git status

On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean
~~~

<br>

#### Staging Area

~~~ bash
git add .
~~~

현재까지 작업중이던 **모든** 파일들을 staging area(무대)에 올립니다.

<br>

#### Commit

```bash
git commit -m "commit message"
```

이 상태가 중요한 것은 나중에 Github에 `push`한 것을 `revert`하고 싶을 때

원래상태로 복구하고 싶은 이 커밋을 찾아서 `push`해야 하기 때문입니다.

"commit message" 부분에는 이 커밋에 대한 보조 설명이라던지 그냥 단순하게 당시 날짜를 적어도 무방합니다.

<br>

#### Remote & Origin (초기 설정에 필요)

~~~bash
git remote add origin git@github.com:avoholo/avoholo.github.io.git
git config user.name "avoholo"
git config user.email "avoholo9@gmail.com"
~~~

만약 local repo에서 remote repo 설정이 안되어 있다면 주소를 명시해서 설정할 수 있습니다.

 ***tip:*** `.git` 을 확인해서 remote repo가 어디로 설정되어있는지 확인하세요.

<br>

#### Branch

~~~bash
git branch -M main
~~~

만약 `master`외에 `main` Branch로 변경하고 싶다면 해당 명령어로 바꿀 수 있습니다. *Default*는 `master` 입니다.

<br>

#### Push

```bash
git push -u origin master
```

`origin`은 처음 git 설정시에 지금 이 **remote** **repository**(원격 저장소)에 붙여준 이름입니다.

단순히 `origin`을 가장 많이 사용하는 것 같지만, github, gitlab 같이 해당 사이트 이름을 붙여줄 수 있습니다.

그러면 한번에 두 가지 사이트에 `origin` 이름만 바꿔서 `push`할 수 있습니다.

<br>

### 3. Remote vs Origin

#### Remote update

remote 환경에서 변경되었던 부분을 up-to date로 업데이트 합니다. 

~~~bash
git remote update && git status
~~~

- `git status -uno` : Tracking 중인 브랜치가 remote 환경과 얼마나 다른지 알려줍니다.
- `git remote -v update` : 어떤 브랜치가 업데이트 되었는지 확인 할 수 있습니다.
- `git git show-branch *master` : 

<br>

### 4. Errors

#### Unsafe Repository

해당 오류를 수정하는 방법은 간단합니다. 이미 로그에도 어떻게 해야할지 나와있습니다.

~~~bash
git config --global --add safe.directory /directory
~~~

~~~bash
git config --global --add safe.directory '%(prefix)///172.*.*.*/Avoholo/Workspace/JeKyll/avoholo.github.io/avoholo_blog'
~~~

<br>

> Related :
> <a href="/git-fundamental-walkthrough">Git Fundamental Walkthrough </a> 




###### Notes
<small id="medium-ref"><sup>[[1]](#medium)</sup> None.</small>

###### Resources
1. [Github Docs](https://docs.github.com/en)