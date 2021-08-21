---
layout: default
title: cd command not found
parent: Docker
grand_parent: Error
---


Table of contents


- TOC
{:toc}


---

# 상황 
docker logrotate 설정을 위해 로그가 쌓여있는 디렉토리로 이동하려는중 오류가 발생하였다.

# 문제
다음과 같이 명령어를 사용하였다.

```
> sudo cd /var/lib/docker/containers/
sudo: cd: command not found
```

cd는 프로그램이 아닌 쉘 내장 명령어이며 sudo는 프로그램에만 적용이 되기 때문에 발생한 오류였다.

```
> type cd
cd is a shell builtin
> type docker
docker is /usr/bin/docker

```

# 해결

`cd /var/lib/docker/containers/` 명령어를 실행하기 위해서는 root 계정으로 전환하면 사용이 가능하다.

```
> sudo -s
> cd /var/lib/docker/containers/
```

sudo 옵션
- -i : root 계정 전환시 root 디렉토리로 이동한다.
- -s : 현재 디렉토리를 유지하여 root 계정으로 전환한다.

# Reference 
https://askubuntu.com/questions/291666/why-doesnt-sudo-cd-var-named-work