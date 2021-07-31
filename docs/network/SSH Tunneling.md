---
layout: default
title: SSH Tunneling
parent: Network
---

# SSH Tunneling

- 그동안 SSH를 원격 접속 용도로만 사용해왔다.
- 하지만 SSH는 기본적인 접속 기능 이외에 Tunneling 기능을 제공한다.
- SSH Tunneling은 Proxy와 비슷하며 SSH 특성상 전달되는 데이터는 모두 암호화 된다.

## Local Port Forwarding

로컬 포워딩에는 다음과 같이 3가지 항목이 필요하다.

- Client : 웹 서버로 접근을 원하는 PC.
- Web Server : 클라이언트가 접근을 원하는 웹 서버, 80번 포트에 바인딩되며 방화벽이 있다.
- SSH Server : 클라이언트가 접근할 웹 서버를 담고 있는 서버, 22번 포트가 열려있다.

위와 같은 상황에서 클라이언트는 웹 서버의 방화벽 때문에 접근을 할 수 없고 SSH Server의 22번 포트로 우회해서 접근한 뒤 80번 포트로 연결하여 웹 서버에 접근할 수 있다. 이것이 바로 포트 포워딩, 터널링 이다.

로컬 포트 포워딩의 사용법은 다음과 같다.

```
$ ssh -L <Local port>:<Remote Server>:<Remote Port> [SSH Server]
```

- -L : Local 포트 포워딩임을 뜻함
- Local port : 클라이언트가 SSH Server로 접근할 포트
- Remote Server : 클라이언트가 접근할 웹 서버 주소
- Remote Port : 웹 서버가 바인딩되어 있는 포트
- SSH Server : SSH Server 주소

다음 예시를 통해 자세히 알아보자.

```
$ ssh -L 8000:127.0.0.1:80 192.168.0.1
```

- 클라이언트는 127.0.0.1 주소를 가진 웹 서버에 접근을 원하며 SSH Srver의 8000번 포트로 요청을 한다.
- 8000번 포트로 우회하여 80번 포트에 바인딩 되어 있고 주소가 127.0.0.1 인 웹 서버를 찾아 연결한다.
