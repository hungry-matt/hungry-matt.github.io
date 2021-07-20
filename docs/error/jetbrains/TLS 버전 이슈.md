---
layout: default
title: TLS 버전 이슈
parent: JetBrains
grand_parent: Error
---

Table of contents

- TOC
{:toc}

---

# 상황
클라우드 MySQL 접속을 위해 DataGrip을 처음 사용해봤다.

# 환경
- DataGrip, version: 2021.1.3
- Mac OS

# 문제
아무 생각없이 최신버전을 받았고 다른 툴과 다를게 없는 서버 호스트 정보를 입력하고 Test Connection를 실행하였는데 아래 메세지와 함께 접속이 되지 않았다. 

```
javax.net.ssl.SSLHandshakeException: No appropriate protocol (protocol is disabled or cipher suites are inappropriate)

The following required algorithms might be disabled: SSLv3, TLSv1, TLSv1.1, RC4, DES, MD5withRSA, DH keySize < 1024, EC keySize < 224, 3DES_EDE_CBC, anon, NULL, include jdk.disabled.namedCurves. Edit the list of disabled algorithms to include required algorithms. You can try to enable TLSv1 or TLSv1.1 first.

JDBC driver may have disabled TLS > 1.1.
```

# 해결
첫 관문부터 심상치 않았지만 부족한 지식을 끌어모아 구글링을 통해 나와 똑같은 상황에 처해 있는 글을 찾았다. TLS 버전과 관련된 이슈였고 아래와 같은 순서로 해결하였다.

## 해결 과정

### 1. java가 설치되어 있는 security 디렉토리에 `custom.java.security` 파일을 생성한다.

- Mac OS 기준이며 Finder로 찾아지지 않아 결국 터미널에서 생성하였다.
```
/Library/Java/JavaVirualMachines/corretto-11.0.11/Contents/Home/lib/security
```

### 2. 파일을 생성하고 다음과 같은 내용을 채워준다.

```
jdk.tls.disabledAlgorithms=SSLv3, TLSv1.1, RC4, DES, MD5withRSA, \
    DH keySize < 1024, EC keySize < 224, 3DES_EDE_CBC, anon, NULL, \
    include jdk.disabled.namedCurves
```

### 3. Data Sources Advanced 탭에서 다음과 같이 VM Option을 추가한다.

```
-Djava.security.properties=${PATH_TO_FILE?}/custom.java.security
```

- ${PATH_TO_FILE}은 생성한 파일의 디렉토리 경로를 채워주면 된다.

- /Library/Java/JavaVirualMachines/corretto-11.0.11/Contents/Home/lib/security/custom.java.security

### 4. 마지막으로 MySQL 버전이 5.7.28 이상인 경우 Advanced 탭에서 옵션을 변경해준다.

```
enabledTLSProtocols = TLSv1.2
```

해당 내용들은 아래 레퍼런스를 통해 작성되었다.

# Reference
- [DataGrip upgraded to 2021.1.2, error occurs when connecting to database](https://intellij-support.jetbrains.com/hc/en-us/community/posts/360010798199-DataGrip-upgraded-to-2021-1-2-error-occurs-when-connecting-to-database-SSLHandshakeException-)