---
layout: default
title: Container xxxx is not running
parent: Docker
grand_parent: Error
---

Table of contents

- TOC
{:toc}

---

# 상황
Docker로 Redis 컨테이너 접속을 시도 하였다.

# 문제
다음과 같이 컨테이너 접속 시도 중 에러가 발생하였다.

```
$ docker exec -it redis_boot redis-cli
Error response from daemon: Container 6c4b72ba306b893d2d522f9ef0ad056dfebd77bbeb5e9035fb6a4ed28e08cb53 is not running
```

# 해결
에러 메세지에 나와있듯이 컨테이너가 실행 되지 않은 상태에서 접속을 시도하여 발생한 에러였다.

### 해결 과정

1. `docker exec` 명령어는 컨테이너가 실행 중일 때 실행하는 명령어이다.

2. 그래서 먼저 `docker ps` 명령어를 통해 접속하고자 하는 컨테이너가 실행 중인지 목록에서 확인 한다.

3. 실행 중인 컨테이너가 없다면 `docker start [CONTAINER]` 명령어를 통해 컨테이너를 실행한다. 

4. 다시 `docker exec` 명령어를 실행하여 컨테이너 접속이 되는지 확인한다.

# Reference
- [docker exec](https://docs.docker.com/engine/reference/commandline/exec/)