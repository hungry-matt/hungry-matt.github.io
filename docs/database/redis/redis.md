---
layout: default
title: Redis
parent: Redis
---

Table of contents

- TOC
{:toc}

---

# Redis

- 레디스는 Remote Dictionary Server의 약자이다.

- In-Memory Database로 모든 데이터를 메모리에 저장하고 조회할 수 있다.

- 관계형 데이터베이스 보다 빠른 이유는 디스크 접근 보다 메모리 접근이 빠르기 때문이다.

## 레디스의 이점

### 다양한 자료구조

단순한 키와 값의 구조가 아닌 다양한 자료구조를 제공함으로써 애플리케이션 요구 사항을 충족시켜줄 수 있는 유연함을 가지고 있다.

#### Strings

- key와 value의 최대 길이는 512MB이다.

- key의 이름이 불필요하게 길어지면 메모리 사용도 커지게 되니 구분자를 사용하면 좋다. (':', '-')

Command | Syntax | Description
---|---|---
Set | key value | 값을 저장한다. 키가 존재한다면 값을 덮어씌운다.
Get | key | 값을 반환한다.

```
> set mykey "hello"
OK
> get mykey
"hello"
```

#### List

- 추가된 순서가 유지되는 문자열 리스트이다.

- linked list의 특징을 가지고 있다.

Command | Syntax | Description
---|----|---
Lpush | key value | 키에 저장된 목록의 처음에 값을 넣는다.
Rpush | key value | 키에 저장된 목록의 마지막에 값을 넣는다.
Lrange | key start stop | 리스트 형식의 값을 start, stop 인덱스를 지정하여 가져올 수 있다.
Lpop | key | 키에 저장된 목록의 첫번째 값을 제거하고 반환한다.
Rpop | key | 키에 저장된 목록의 마지막 값을 제거하고 반환한다.

Command | List | Console
```
> lpush item A // A
(integer) 1
> lpush item B // B, A 
(integer) 2
> rpush item C // B, A, C
(integer) 3
> lpush item D // D, B, A, C
(integer) 4
> lpop item // B, A, C
"D"
> rpop item // B, A
"C"
```

# Reference

- [redis](https://redis.io/commands#)