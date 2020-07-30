---
layout: post
title: "2020.07.09 - 개발 일지"
author: "rmfls2017"
categories: developments
tags: [developments, docker, python, docker-compose]
image: development-journal.jpg
---

# 도커 삽질기 2탄

바로 다음날에 작성하려했지만 계획이 꼬여서 6일이 지난 지금에야 작성한다. (다 핑계...)

## 이전 작성글

-   [도커 삽질기 1탄](./development-5.md)

## 삽질 스타트

앞서 1탄에서 얘기했던 dockerhub 에서 제공하는 파이썬 기본 이미지를 통해서 바탕이 되는 이미지를 찾았다.

그 이후에는 ubuntu 에서 패키지 관리를 담당하는 apt-get(Advanced Packaging Tool) 를 먼저 업데이트 하고 패키지를 설치하기 위한 몇 가지의 옵션들을 알아보았다.

이제는 어떤 패키지를 설치하고 그 다음 개발 과정을 어떻게 진행할 지 계획하는 순서가 되겠다.

먼저 ubuntu 환경에서 python 개발을 위한 사전 설치 패키지가 있는데 본인의 대표 케이스는 `build-essential` 이였다.

해당 패키지는 c, c++ 개발에 필요한 패키지이지만 python 을 개발하면서 필요하기 때문이다. 그 이유는 다음의 글을 확인하자.

> To successfully install C extension packages like lxml from source into a virtual environment, you also need the necessary build tools like gcc or clang.

c 기반으로 개발되어 있는 패키지를 가상환경에서 실행하기 위해서는 build-essential 의 도구가 필요하기 때문에 설치해야한다는 내용이다. (왜 python 에 c, c++ 를 빌드하는 패키지가 필요한지에 대해 묻는다면 [링크](https://softwareengineering.stackexchange.com/questions/20988/why-is-python-written-in-c-and-not-in-c) 를 확인하길 바란다.)

그리고 dockerfile 을 만들 때는 항상 이미지의 크기를 줄여야 효율적이므로 container 안에서 설치한 패키지들의 임시 파일들을 삭제하는 과정까지 진행한다.

```shell
$ rm -rf /var/lib/apt/lists/*
```

위의 경로에는 apt-get 을 사용하여 설치한 패키지들이 저장되는 곳으로 이미 설치가 끝난 상태에서 다운로드 파일을 계속해서 들고 있을 필요는 없으므로 제거해준다.

이 때 _RUN_ 명령어가 실행될 때 이전에 말했던 것 처럼 한 줄, 한 줄 실행이 되는데 하나의 패키지를 하나의 _RUN_ 명령어로 처리하게 된다면 이미지가 하나 하나 쌓여 최종적인 이미지의 크기가 늘어나기 때문에 필요한 패키지를 하나의 RUN 명령어로 실행한다. (docker layer 와 관련된 내용)

docker image 의 layer 가 어떻게 동작하는지 설명하는 것은 이 글의 방향과 맞지 않으므로 공부하기에 좋은 블로그 글을 하나 첨부하겠다. (http://cloudrain21.com/examination-of-docker-containersize-ufs)

```shell
$ api-get install build-essential \
libproj-dev \
... \
&& rm -rf /var/lib/apt/lists/*
```

마지막에 `&&` 는 명령어를 다 실행하고 난 다음에 이어서 실행하기 위한 bash 실행 명령어다. (파이프 `|` 와는 다른 개념)

다음은 1탄에 이어서 이번 편까지 필요로 하는 명령어와 개발을 위해 준비단계를 적은 Dockerfile 이다.

```dockerfile
FROM python:3.7.7-slim-buster

RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y -qq --no-install-recommends \
    apt-utils \
    build-essential \
    libproj-dev \
    && rm -rf /var/lib/apt/lists/*
```

---

## 다음 예고편

다음에는 django 의 실질적인 개발을 위해 필요로 하는 명령어 및 패키지들을 알아보겠다.

## Reference

-   https://askubuntu.com/questions/179955/var-lib-apt-lists-is-huge
-   https://softwareengineering.stackexchange.com/questions/20988/why-is-python-written-in-c-and-not-in-c
-   https://www.44bits.io/ko/post/how-docker-image-work
-   http://cloudrain21.com/examination-of-docker-containersize-ufs
