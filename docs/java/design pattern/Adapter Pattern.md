---
layout: default
title: Adapter Pattern
parent: Design Pattern
grand_parent: Java
---


Table of contents


- TOC
{:toc}


---

# Adapter Pattern

- 어댑터 패턴은 호환되지 않는 두 인터페이스 사이에서 커넥터 역할을 한다.

- 이렇게 사용되는 이유는 기존 인터페이스를 클라이언트가 기대하는 새로운 인터페이스로 구현하기 위해서이다.

## Adapter Pattern Example

- 고급 자동차의 최고 속도를 마일(MPH)로 표시되는 기능이 있다고 가정하자.

- 이 기능을 시간당 킬로미터(km/h) 단위로 변환해야할 때 어댑터 패턴을 사용해 보자.

- 먼저 시간당 속도를 마일로 표시하는 Movable 인터페이스를 만든다.

```java
public interface Movable {
    //return speed in MPH
    double getSpeed();
}
```

- 다음은 Movable 인터페이스를 구현한 구현체를 만들어 주자.

```java
public class BugattiVeyron implements Movable {
    @Override
    public double getSpeed() {
        return 268;
    }
}
```
- 이제 시간당 킬로미터를 반환해주는 기능을 가진 MovableAdapter 인터페이스를 만든다.

```java
public interface MovableAdapter {
    //return speed in km/h
    double getSpeed();
}
```

- MovableAdapter 인터페이스를 구현하며 MPH를 KM/H로 변환해주는 전용 메서드를 만들어준다.

```java
public class MovableAdapterImpl implements MovableAdapter {
    private final Movable movable;

    public MovableAdapterImpl(Movable movable) {
        this.movable = movable;
    }

    @Override
    public double getSpeed() {
        return convertMPHtoKMPH(movable.getSpeed());
    }

    public double convertMPHtoKMPH(double mph) {
        return mph * 1.60934;
    }
}
```

- 구현된 기능을 통해 속도 변환 테스트를 해보면 참이 나오게 된다.

```java
@Test
void convert() {
    Movable bugattiVeyron = new BugattiVeyron();
    MovableAdapter movableAdapter = new MovableAdapterImpl(bugattiVeyron);

    assertEquals(bugattiVeyron.getSpeed(), 268.0);

    //assertEquals(expected, actual, delta)
    //delta: 실수를 계산할 때는 오차가 발생할 수 있다. delta는 두 값을 비교시 허용할 오차 범위를 정할 수 있다.
    //0.00001은 0~0.00001 까지의 범위를 가진다.
    assertEquals(movableAdapter.getSpeed(), 431.30312, 0.00001);
}
```

## When to Use Adapter Pattern

- 외부 라이브러리를 재사용하고 싶지만 현재 애플리케이션과 호환되지 않는 경우

- 애플리케이션이 클라이언트가 기대하는 인터페이스와 호환되지 않는 경우

- 원본 코드를 수정하지 않고 애플리케이션 코드를 재사용하려는 경우

# Reference
- [The Adapter Pattern in Java](https://www.baeldung.com/java-adapter-pattern)