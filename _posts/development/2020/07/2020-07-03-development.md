---
layout: post
title: "2020.07.03 - 개발 일지"
author: "rmfls2017"
categories: developments
tags: [developments, docker, python, docker-compose]
image: development-journal.jpg
---

과제로 주어졌던 프로젝트에서 docker 에 대해서 좀 더 공부하기로 하고 계속해서 진행

dockerfile 과 compose 를 이용하여 service 와 db 를 연결하기 위해서 공부 및 삽질을 시작했다.

## 삽질 스타트

먼저 Dockerfile 을 작성하는 것 부터 시작이였다.

어떠한 이미지를 통해서 django 서비스를 띄울 것인가?

docker hub 에서 찾아보니 공식 django 이미지와 python 이미지가 있었다.

django 이미지를 다운받아서 사용을 한다면 django application 을 수정하고 작동하는 것이 아닌 단순히 서비스하는 것에 의의를 두는 것 같아 python 이미지를 다운받고 django 를 설치하기로 했다 (pip install django)

그리하여 선택한 이미지는 `python:3.7.7-slim-buster` 였다.

### 이미지가 의미하는 것

이미지가 뜻하는 바를 천천히 살펴보면

제일 앞에 위치해 있는 `python:3.7.7` 은 언어와 그 언어의 버전을 의미한다

만약에 2.7 버전을 사용한다면 `python:2.7` 이 될 것이다. (파이썬 2.7 버전을 이미지로 제공하지만 생태계와 발전 속도를 고려하면 3 버전으로 시작하는 것이 좋다고 생각한다)

그에 따라오는 `slim` 은 이미지를 줄이기 위함이다.

개발함에 있어서 사용하지 않은 패키지, 코드들은 굉장히 악취를 풍기기 마련이다.

이러한 이유로 정말 필요한 패키지를 제외한 나머지 부가적인 패키지들을 제외한 상태로 사용하는 것이 `slim` 으로 생각하면 되겠다.

마지막으로 `burster` 의 의미는 dockerhub 에서 알려주는 내용을 가져오겠다

> Some of these tags may have names like buster or stretch in them. These are the suite code names for releases of Debian and indicate which release the image is based on. If your image needs to install any additional packages beyond what comes with the image, you'll likely want to specify one of these explicitly to minimize breakage when there are new releases of Debian.

가장 중요한 것은 `These are the suite code names for releases of Debian and indicate which release the image is based on.` 

Debian OS 의 릴리즈 버전 이름을 나타낸다고 생각하면 될 것이다.

---

Dockerfile 에서 사용하고자 하는 이미지를 바탕으로 빌드하려면 시작을 *FROM* 으로 시작하여 이미지를 만든다.

```dockerfile
FROM python:3.7.7-slim-buster
```

그리고 이제부터가 진짜 시작이다 라고 생각하면 되겠다.

이 이미지를 바탕으로 어떤 것을 설치할 것인지, 또 어떻게 실행할지 계획을 세우는 것이 중요하다.

예를 들어, 필자의 경우는 단순히 다른 블로그를 통해 필요로하는 패키지를 막 가져다가 설치하고 했는데 이렇게 될 경우 `slim` 을 사용한 의미가 없지 않겠는가?

그리하여 사용하고자 하는 패키지들 그리고 debian 에서 설치해야할 패키지들을 알아보자

---

먼저 이미지를 빌드할 때 실행할 명령어를 적기 위해서는 *RUN* 명령어를 사용한다

```dockerfile
RUN apt-get updates
```

이 명령어는 

```shell
$ sudo apt-get updates 
```

와 같은 의미가 된다.

기본적으로 dockerfile 을 실행하는 주체는 root 로 설정되어 실행되므로 이 점을 유의하자.

그리고 *RUN* 명령어를 실행하면서 대부분 dockerfile 초입에 debian pacakge 를 설치하는데 install 앞에 다음의 명령어를 사용하면 유용하다

```shell
$ sudo DEBIAN_FRONTEND=noninteractive apt-get install -y -q XXX
```

DEBIAN_FRONTEND=noninteractive 가 의미하는 바는 하나의 단락을 가져오겠다.

> Configuration prompts are great if you are interacting as a knowledgeable user, but in some cases, particularly in automation or scripting, you don’t want to prompt the user at all. In this case, quieting the configuration prompts is likely advantageous. To do this, run your apt command with the environment variable specified before it.

여기에서도 중요한 부분은 `you don’t want to prompt the user at all. In this case, quieting the configuration prompts is likely advantageous.`

프롬포트 결과에 대해서 항상 상호작용을 원치 않기 위해, 한마디로, 단순히 설치만하고 사용자가 추가적인 명령어를 입력하기 않기 위함이다.

몇 몇은 궁금해 할 것이다. 설치하는데 추가적인 입력사항이 필요 없지 않나? 만약 시스템과 밀접한 패키지를 설치하게 될 경우 경로, 사용자, 그룹과 관련한 것들에 대해서 따로 설정하게 될 경우에 맨 앞 부분의 명령어를 사용하게 될 경우 무시하게 되고 그로 인해 발생할 수 있는 문제가 있을 것이다.

---

글을 적다보니 상당히 양이 많아져서 글을 분리해서 올려야 읽기가 쉬울 것 같아서 이 쯤에서 마무리 하고 다음 글에 이어서 적도록 하겠다.

## Reference

* [linux debian](https://linuxhint.com/debian_frontend_noninteractive/)
* [dockerhub python](https://hub.docker.com/_/python)
* [docker-compose 를 사용하여 서비스 올리기](https://docs.docker.com/compose/django/)