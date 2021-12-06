---
layout: default
title: Transactional(Spring vs JTA)
parent: Spring
---

# Transactional(Spring vs JTA)

`org.springframework.transaction.annotation.Transactional`과  `javax.transaction.Transactional annotations`의 차이점과 어떤 상황에서 사용해야 하는지 알아본다.

## 차이점

- Isolation : Spring은 트랙잭션의 격리 범위를 제공하지만 JTA는 기본적인 수준만 사용할 수 있다.

- Propagation : Spring과 JTA에서 각 각 제공하는 전파 유형을 사용할 수 있다. Spring은 `Nested` 추가 전파 유형을 제공한다.

- ReadOnly : Spring에서만 readOnly 속성을 제공한다.

- Timeout : Spring에서만 Timeout 속성을 제공한다.

- Rollback : Spring과 JTA는 각 각의 롤백 기능을 제공한다. JTA는 `rollbackOn` 및 `dontRollbackOn` 속성을 제공하며 Spring은 `rollbackFor` 및 `noRollbackFor` 속성을 제공한다.

## 적용 및 호환성

JTA의 트랜잭션 애노테이션은 Java EE 사양에 맞는 Bean 및 클래스에 적용되는 반면 Spring의 트랜잭션 애노테이션은 Spring Bean에서만 적용된다.

# Reference

- https://www.baeldung.com/spring-vs-jta-transactional