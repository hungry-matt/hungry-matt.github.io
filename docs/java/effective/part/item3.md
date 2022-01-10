---
layout: default
title: item 3. private 생성자나 열거 타입으로 싱글턴임을 보증하라
parent: Part1
grand_parent: Effective
---


Table of contents


- TOC
{:toc}


---

# private 생성자나 열거 타입으로 싱글턴임을 보증하라

싱글턴이란 인스턴스를 오직 하나만 생성할 수 있는 클래스를 말한다.

## 싱글턴 사용 이유

- 한 번의 객체 생성으로 재사용이 가능하기 때문에 메모리 낭비를 방지할 수 있다.

- 오직 하나만 존재하기 때문에 자원을 공유할 수 있는 장점이 있다.

## 싱글턴을 만드는 방식

### public static 멤버가 final 필드인 방식

private 생성자는 public static final 필드인 `Elvis.INSTANCE`를 초기화할 때 딱 한 번만 호출된다. public이나 protected 생성자가 없으므로 인스턴스가 전체 시스템에서 하나뿐임이 보장된다.

```java
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();

    private Elvis() {
        ...
    }

    public void leaveTheBuilding() {
        ...
    }
}
```

장점
- 해당 클래스가 싱글턴임이 API에 명백히 드러난다. public static 필드가 final이니 절대로 다른 객체를 참조할 수 없다.

- 간결하다.

#### 예외

리플렉션 API인 `AccessibleObject.setAccessible`을 사용해 private 생성자를 호출할 수 있다.

#### 해결

이러한 공격을 방어하려면 생성자를 수정하여 두 번째 객체가 생성되려할 때 예외를 던지면 된다.

```java
private Elvis() {
    if (INSTANCE != null) {
        throw new RuntimeException("생성자를 호출 할 수 없습니다.");
    }
}
```

### 정적 팩터리 메서드를 public static 멤버로 제공

`Elvis.getInstance`는 항상 같은 객체의 참조를 반환하므로 제2의 인스턴스가 만들어지는걸 방지할 수 있다. (리플렉션을 통한 예외는 똑같이 적용된다.)

```java
public class Elvis {
    private static final Elvis INSTANCE = new Elvis();

    private Elvis() {
        ...
    }

    public static Elvis getInstance() {
        return INSTANCE;
    }

    public void leaveTheBuilding() {
        ...
    }
}

```

장점

- API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다.

- 원한다면 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있다.

- 정적 팩터리의 메서드 참조를 공급자(supplier)로 사용할 수 있다.

### 두 방식의 문제점

싱글턴 클래스를 직렬화 후 역직렬화할 때 새로운 인스턴스가 만들어진다. 역직렬화는 기본 생성자를 호출하지 않고 값을 복사해서 새로운 인스턴스를 반환한다. 

이를 방지하기 위해 readResolve() 메서드를 추가하여 싱글턴 인스턴스를 반환하고 모든 필드에 transient(직렬화 제외) 키워드를 넣는다.

다음 코드는 역직렬화 시 반드시 호출되는 readResolve 메서드이다.

```java
private Object readResolve() {
    return INSTANCE;
}
```

### 원소가 하나인 열거 타입을 선언

pulbic 필드 방식과 비슷하지만, 더 간결하고, 추가 노력 없이 직렬화할 수 있고, 리플렉션 공격에서도 제2의 인스턴스가 생기는 일을 완벽히 막아준다.

`대부분 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법이다.`

단, 싱글턴이 Enum 외에 클래스를 상속해야 한다면 이 방법은 사용할 수 없다.

```java
public enum Elvis {
    INSTANCE;

    public void leaveTheBuilding() {
        ...
    }
}
```