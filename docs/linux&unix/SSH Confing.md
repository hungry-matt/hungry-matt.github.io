---
layout: default
title: SSH Config
parent: Linux&Unix
---


Table of contents


- TOC
{:toc}


---

# SSH Config

복수의 SSH 키를 사용하고 있을때 SSH 접속시 매번 명령어를 작성해야 하는 불편함이 있다. 이러한 점을 SSH Config 파일을 통해 해결할 수 있다.

먼저 .ssh 디렉토리에 config 파일을 생성한다.

```sh
> cd ~/.ssh
> vi config
```

생성된 config 파일에는 다음과 같이 접속 정보를 입력한다.

```
Host privatehost
 HostName 10.3.25.100
 User test
 IdentityFile ~/.ssh/key.pem
 ProxyCommand ssh publichost -W %h:%p

Host publichost
 HostName 5.15.123.12
 User test
 IdentityFile ~/.ssh/key.pem
 ProxyCommand none

Host host
 HostName 52.3.123.23
 User test
 IdentityFile ~/.ssh/key.pem
```

- Host: ssh 명령에 사용되는 이름이다. ssh로 접속시 `>ssh privatehost` 명령어로 접속할 수 있다.
- HostName: Host 이름과 매핑되며 실제 호스트명이 들어간다.
- User: 접속할 호스트의 계정 ID에 해당한다.
- IdentityFile: 인증 키로 접속해야할 경우 지정된 키파일의 경로 작성한다. 인증키가 필요하지 않을 경우 생략이 가능하다.
- ProxyCommand: 외부에서 접근할 수 없는 내부망은 외부로 열려있는 서버에 접속후 내부 서버로 이동해야하는데 ProxyCommand 옵션으로 간단히 설정할 수 있다.
    - 옵션에서 %h는 연결할 호스트 이름이고 %p는 연결할 호스트의 포트이다.