---
layout: post
title: "2020.06.29 - 개발 일지"
author: "rmfls2017"
categories: developments
tags: [developments]
image: development-journal.jpg
---

## Coding Test 준비를 위한 노오오오력

### Scikit learn (사이킷런)

코딩과제를 통해 처음 알게 된 파이썬 패키지다.

과제로 데이터 파일을 전달 받았는데 사이킷런을 통해서 파일을 읽고 풀 수 있다고 설명을 해주었다.

이전에 django 와 drf (django rest framework) 를 사용하여 api 를 만드려고 계획을 했었는데 아무래도 본인이 잘 알지 못 하는 Frontend skill 도 같이 요구되었다. (Vue.js, React ...)

Web Fullstack 을 지원하진 않앗지만 회사에서 요구하는 사항자체를 따지면 (구체적인 요구 사항은 언급하지 않겠다) 특정 날짜를 기준으로 잡고 상, 하로 스크를 하게 될 경우로 나뉘었다

`아래에서 위로 => 다음 데이터 로드`

`위에서 아래로 => 이전 데이터 로드`

언급했던 행동에 대해서 계획을 잡았고 DB table 까지 구상을 마쳤지만 Dockerfile 에서 애를 먹었다.

`Dockerfile` 작성을 다 마쳤지만 *docker-compose.yml* 에서 계속해서 생각치 못한 오류를 해결하느라 시간을 많이 보냈다.

정확히는 db container 를 mysql 이미지를 통해서 생성하는데 환경설정 파일을 django 에서 사용하는 파일처럼 동일하게 `.db.env` 로 설정하였다가 계속해서 환경변수 인자가 들어가지 않았다.

문제가 되었던 docker-compose.yml 부분은 다음과 같다

- docker-compose.yaml

```yaml
  mysql-db:
    image: mysql:5.7
    command: mysqld --character-set-server=utf8 --collation-server=utf8_general_ci
    env_file:
      - .db.env
    restart: always
    ports:
      - "3306:3306"
```

env_file 에서 `.db.env` 부분이 제대로 구동이 되지 않았다.

파일명때문에 읽히지 않은 것인가 하는 이유가 있었는데 이러한 가정을 세우고 나서 container 를 빌드하게 되면 파일 전체를 읽지 못해야 하는 것 아닌가 라는 의문이 들었다.

- .env file

```
MYSQL_ROOT_PASSWORD=XXX
MYSQL_USER=XXX
MYSQL_PASSWORD=XXX
MYSQL_DATABASE=XXX
```

database 와 user, root password 까지는 잘 설정이 되었지만 password 에서 작동하지 않았다.

정확한 원인은 알 수 없었지만 이상한 것은 `db.env` 파일로 수정 후 명령어 실행하면

```bash
$ docker-compose up --build
```

사용자가 잘 만들어지고 패스워드도 잘 설정이 된다. (?)

docker-compose 에서 `env_file` 로 parameter 를 받게 될 경우 `.env` 파일을 찾는다고한다.

그 중에 본인은 .db.env 파일로 설정하여 파일명으로 인한 에러로 판단이 든다.

### Reference

* https://github.com/docker/compose/issues/4223
* https://docs.docker.com/compose/env-file/
