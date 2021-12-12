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

### 1. Defalut

DB의 Isolation Level을 따른다.

mysql, postgresql 의 Isolation Level 확인 방법
```
-- mysql
show variable like '%isolation'; -- default : REPEATABLE-READ

--postgresql
select current_setting('transaction_isolation'); -- defalut : READ COMMITTED

```

### 2. READ_UNCOMMITTED

- 트랜잭션이 처리중인 혹은 아직 커밋되지 않은 데이터를 다른 트랜잭션이 읽는 것을 허용한다.

- A라는 데이터를 B라는 데이터로 변경하는 동안 다른 사용자가 아직 변경되지 않은(uncommitted or dirty) B 데이터를 읽을 수 있다.

> Drity Read : 트랜잭션이 완료하지 않은 작업을 다른 트랜잭션이 볼 수 있는 현상을 말한다.

### 3. READ_COMMITTED

- 트랜잭션이 커밋되어 확정된 데이터만 읽는 것을 허용한다. (Dirty Read 방지)

- A라는 데이터를 B라는 데이터로 변경이 완료될 때까지 접근을 제한한다.

### 4. REPEATABLE_READ

- 트랜잭션 작업이 완료될 때까지 SELECT 문장이 사용하는 모든 데이터에 shared lock이 걸리므로 다른 트랜잭션은 해당 데이터의 수정이 불가능하다.

- 선행 트랜잭션의 작업이 완료될 때까지 후행 트랜잭션은 해당 데이터의 갱신이나 삭제가 불허하므로 같은 데이터를 두번 쿼리 했을 때 일광성 있는 결과를 리턴한다.

### 5. Serializable

- 가장 높은 격리, 성능 저하의 우려가 있다.

- 데이터의 일괄성 및 동시성을 위해 MVCC(Multi Version Cocurrency Control)을 사용하지 않는다.

> MVCC : 다중 사용자 데이터베이스 성능을 위한 기술로 데이터 조회 시 Lock을 걸지 않고 데이터의 버전을 관리해 일광성 및 동시성을 높이는 기술이다.

- 트랜잭션이 완료될 때까지 SELECT 문장이 사용하는 데이터에 shared lock을 걸리므로 다른 트랜잭션이 해당 데이터에 대한 수정이나 입력이 불가능하다.

