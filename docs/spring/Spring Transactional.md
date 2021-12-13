---
layout: default
title: Spring Transactional
parent: Spring
---

# Spring Transactional

`Transaction`은 데이터베이스의 상태를 변경하는 작업이나 한번에 수행되어야 하는 연산들을 의미한다.
한 트랜잭션에서 수행되는 작업은 하나의 단위로 처리한다.

Spring에서 제공하는 Transactional의 기능에 대해 알아본다.

## Isolation

격리 수준의 옵션이다. 트랜잭션에서 일광성이 없는 데이터를 허용하는 수준을 말한다.


### 트랜잭션 격리의 필요성

- 한 트랜잭션이 수행되는 동안 다른 트랜잭션이 관여하지 못하도록 하는것을 Locking이라 한다.

- 무조건적인 Locking은 동시에 수행되는 트랜잭션이 순차적으로 처리하는 방식으로 구현되기에 DB의 성능이 떨어지게 된다.

- 반대로 성능을 높이기 위해 Locking의 범위를 줄이게 되면 데이터 무결성에 문제가 발생할 수 있다.

- 격리 레벨은 올라갈수록 비용이 높아진다.

- 그래서 상황에 따라 효율적인 격리 설정이 필요하다.


### 1. Defalut

DB의 Isolation Level을 따른다.

mysql, postgresql 의 Isolation Level 확인 방법
```
-- mysql
show variable like '%isolation'; -- default : REPEATABLE-READ

--postgresql
select current_setting('transaction_isolation'); -- defalut : READ COMMITTED

```

### 2. READ_UNCOMMITTED (Level 0)

- 트랜잭션이 처리중인 혹은 아직 커밋되지 않은 데이터를 다른 트랜잭션이 읽는 것을 허용한다.

- SELECT 수행 시 해당 데이터에 Shared Lock(Read Lock)이 걸리지 않는 Level.

- A라는 데이터를 B라는 데이터로 변경하는 동안 다른 사용자가 아직 변경되지 않은(uncommitted or dirty read) B 데이터를 읽을 수 있다.

### 3. READ_COMMITTED (Level 1)

- 트랜잭션이 커밋되어 확정된 데이터만 읽는 것을 허용한다. (Dirty Read 방지)

- SELECT 수행 시 해당 데이터에 Shared Lock이 걸리는 Level.

- A라는 데이터를 B라는 데이터로 변경이 완료될 때까지 접근을 제한한다.

### 4. REPEATABLE_READ (Level 2)

- 트랜잭션 작업이 완료될 때까지 SELECT가 수행하는 모든 데이터에 Shared Lock이 걸리므로 다른 트랜잭션은 해당 데이터의 수정이 불가능하다.

- 트랜잭션이 범위 내에서 조회한 데이터의 내용이 항상 동일함을 보장한다.

- 선행 트랜잭션의 작업이 완료될 때까지 후행 트랜잭션은 해당 데이터의 갱신이나 삭제가 불허하므로 같은 데이터를 두번 쿼리 했을 때 일광성 있는 결과를 리턴한다.

- 한 트랜잭션에서 10개의 데이터를 가진 테이블에서 1 과 5 레코드를 조회 시 다른 트랜잭션에서는 1 과 5 레코드의 갱신이 불가능하다. 이를 제외한 다른 범위에 대한 데이터 삽입은 가능하다.

### 5. Serializable (Level 3)

- 가장 높은 격리, 성능 저하의 우려가 있다.

- 데이터의 일괄성 및 동시성을 위해 MVCC(Multi Version Cocurrency Control)을 사용하지 않는다.

> MVCC : 다중 사용자 데이터베이스 성능을 위한 기술로 데이터 조회 시 Lock을 걸지 않고 데이터의 버전을 관리해 일광성 및 동시성을 높이는 기술이다.

- 트랜잭션이 완료될 때까지 SELECT가 수행하는 모든 데이터에 Shared Lock이 걸리므로 다른 트랜잭션이 해당 데이터에 대한 수정이나 입력이 불가능하다.

## 격리 수준에 따라 발생되는 현상

Isolation Level | Dirty Read | Non Repeatable Read | Phantom Read
---|---|---|---
Read Uncommitted | O | O | O
Read Committed | | O | O
Repeatable Read | | | O
Serializable | | | 

### 1. Dirty Read

- 커밋되지 않은 데이터를 다른 트랜잭션이 읽을 수 있도록 허용할 때 발생되는 현상.

- 트랜잭션이 완료하지 않은 작업을 다른 트랜잭션이 읽을 때 나타난다.

### 2. Non Repeatable Read

- 한 트랜잭션에서 같은 쿼리를 두 번 수행할 때, 두 쿼리의 결과가 상이하게 나타나는 현상.

- 한 트랜잭션이 수행중일 때 다른 트랜잭션이 데이터를 수정 또는 삭제했을 때 나타난다.

### 3. Phantom Read

- 한 트랜잭션에서 같은 쿼리를 두 번 수행할 때, 첫번째 쿼리에서 없던 데이터가 두번째 쿼리에서 나타나는 현상.

- 한 트랜잭션이 수행중일 때 다른 트랜잭션에서 데이터를 삽입 했을 때 나타난다.





