---
layout: post
title: "2020.06.17 - 개발 일지"
author: "rmfls2017"
categories: developments
tags: [developments]
image: development-journal.jpg
---

```
$ pip install mysqlclient
```

django 와 mysqlclient 를 연동하면서 생긴 에러내용을 기억하기 위함

python 가상환경 (python3) 을 만들고 그 위에서 설치하려는 과정에서 해당 에러가 발생

첫번째 Error 내용:

```
ERROR: Command errored out with exit status 1:
    command: /Documents/project/server/venv/bin/python3 -c 'import sys, setuptools, tokenize; sys.argv[0] = '"'"'/tmp/pip-install-uhqxdpew/mysqlclient/setup.py'"'"'; __file__='"'"'/tmp/pip-install-uhqxdpew/mysqlclient/setup.py'"'"';f=getattr(tokenize, '"'"'open'"'"', open)(__file__);code=f.read().replace('"'"'\r\n'"'"', '"'"'\n'"'"');f.close();exec(compile(code, __file__, '"'"'exec'"'"'))' egg_info --egg-base pip-egg-info
        cwd: /tmp/pip-install-uhqxdpew/mysqlclient/
Complete output (12 lines):
/bin/sh: 1: mysql_config: not found
/bin/sh: 1: mariadb_config: not found
/bin/sh: 1: mysql_config: not found
Traceback (most recent call last):
    File "<string>", line 1, in <module>
    File "/tmp/pip-install-uhqxdpew/mysqlclient/setup.py", line 16, in <module>
    metadata, options = get_config()
    File "/tmp/pip-install-uhqxdpew/mysqlclient/setup_posix.py", line 61, in get_config
    libs = mysql_config("libs")
    File "/tmp/pip-install-uhqxdpew/mysqlclient/setup_posix.py", line 29, in mysql_config
    raise EnvironmentError("%s not found" % (_mysql_config_path,))
OSError: mysql_config not found
----------------------------------------
ERROR: Command errored out with exit status 1: python setup.py egg_info Check the logs for full command output.
```

`setuptools` 및 그 외 필요한 dev package 설치를 위해 아래의 명령어 실행

```
$ sudo apt-get install build-essential binutils libproj-dev libcurl4-openssl-dev 
```

설치하는 패키지 내용은 각 설명을 참고하자

* [build-essential](https://packages.ubuntu.com/bionic/build-essential)
    
    C, C++ 의 기본적인 header 파일들을 설치

* [binutils](https://packages.ubuntu.com/bionic/binutils)

    GNU 어셈블러, 링커 및 이진 유틸리티

* [libproj-dev](https://packages.ubuntu.com/bionic/libproj-dev)

    지도 투영법 라이브러리 (개발 파일)

* [libcurl4-openssl-dev](https://packages.ubuntu.com/bionic/libcurl4-openssl-dev)

    libcurl (OpenSSL flavour)의 개발 파일 및 문서


두번째 Error 내용:

```
sh: 1: mysql_config: not found
sh: 1: mariadb_config: not found
sh: 1: mysql_config: not found
Traceback (most recent call last):
    File "<string>", line 1, in <module>
    File "/tmp/pip-build-yjyCtV/mysqlclient/setup.py", line 16, in <module>
    metadata, options = get_config()
    File "setup_posix.py", line 61, in get_config
    libs = mysql_config("libs")
    File "setup_posix.py", line 29, in mysql_config
    raise EnvironmentError("%s not found" % (_mysql_config_path,))
EnvironmentError: mysql_config not found
```

첫번째 에러에서 발생한 문제 중에 setuptools 의 문제는 해결되었지만 mysqlclient 는 여전히 문제

### Prerequisites

mysqlclient 를 설치하기 위해선 먼저 설치해야할 패키지들이 있음 (이 부분에서 본인은 확인을 제대로 안하고 무작정 설치하려고 했음...)

* [mysqlclient 설치하기](https://pypi.org/project/mysqlclient/)

> NOTE: Wheels for Windows may be not released with source package. You should pin version in your requirements.txt to avoid trying to install newest source package

