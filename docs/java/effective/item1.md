---
layout: default
title: item1
parent: Effective
grand_parent: Java
---


Table of contents


- TOC
{:toc}


---

# 생성자 대신 정적 팩터리 메서드를 고려하라.

## 핵심 요약

```
정적 팩토리 메서드와 public 생성자는 각자의 쓰임새가 있으니 상대적인 장점을 이해하고 사용하는 것이 좋다. 그렇다 하더라도 정적 팩터리를 사용하는게 유리한 경우가 더 많으므로 무작정 public 생성자를 제공하던 습관이 있다면 고치자.
```

## 기본적인 public 생성자

클래스의 인스턴스를 얻는 가장 쉬운 방법은 public 생성자이다.

```java
class Recipe {
    public Recipe() {
    }
}

Recipe recipe = new Recipe();
```

## 정적 팩터리 메서드

정적 팩터리 메서드는 static 키워드로 생성 되며 클래스를 인스턴스화 하지 않아도 사용할 수 있는 메서드이다.

클래스는 생성자 외에 정적 팩토리 메서드를 제공할 수 있다. 클래스의 인스턴스를 반환하는 단순한 정적 메서드 말이다.

```java
class Recipe {
    private String name;

    private Recipe(String name) {
        this.name = name;
    }

    public static Recipe valueOf(String name) {
        return new Recipe(name);
    } 
}

Recipe recipe = Recipe.valueOf("noodle");
```

## 정적 팩터리 메서드의 장점

### 1. 이름을 가질 수 있다.

생성자에 넘기는 매개변수와 생성자 자체만으로 반환될 객체의 특성을 제대로 설명하지 못한다. 생성자가 어떤 역할을 하는지 정확히 기억하기 어려워 잘못된 생성자를 호출하는 실수를 할 수 있다. 반면 정적 팩터리 메서드는 이름만 잘 지으면 반환될 객체의 특성을 쉽게 묘사할 수 있다.

### 2. 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.

정적 팩터리 메서드 호출을 위해 새로운 인스턴스를 생성하지 않기 때문에 인스턴스를 캐싱하여 재활용하는 식으로 불필요한 객체 생성을 피할 수 있다. 대표적인 예로 `Boolean.valueOf(boolean)` 메서드는 객체를 생성하지 않고 사용할 수 있다. 같은 객체가 자주 사용되는 상황이라면 성능을 상당히 끌어올려 준다.

### 3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.

반환할 객체의 클래스를 자유롭게 선택할 수 있는 유연성을 가지고 있다. 이 유연성을 응용하면 반환될 객체의 구현체를 공개하지 않고도 그 객체를 반환할 수 있어 API를 작게 유지할 수 있다.

다음은 `java.util.Arrays` 클래스의 정적 메서드 `asList()` 이다.

```java
public static <T> List<T> asList(T... a) {
    return new ArrayList<>(a);
}
```

List Collection을 사용하기 위해 하위 구현체인 ArrayList를 반환한다. 이처럼 정적 팩터리 메서드 내부의 구현체를 몰라도 사용할 수 있다.

### 4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.

반환 타입의 하위 타입이기만 하면 어떤 클래스의 객체를 반환하든 상관없다. 이를 통해 정적 팩토리 메서드가 반환하는 인스턴스가 어떤 클래스인지 알 수도 없고, 알 필요도 없어졌다.

다음은 매개변수에 따라 하위 타입을 반환하는 정적 팩터리 메서드를 `of()` 이다.

```java
public interface Executor {
    static Executor of(int size) {
        Executor executor;

        if (size > 100) {
            executor = new JumboExecutor();
        } else {
            executor = new RegularExecutor();
        }

        return executor;
    }

    String getName();

    class RegularExecutor implements Executor {
        @Override
        public String getName() {
            return "RegularExecutor";
        }
    }

    class JumboExecutor implements Executor {
        @Override
        public String getName() {
            return "JumboExecutor";
        }
    }

}

public class Main {
    public static void main(String[] args) {
        Executor regularExecutor = Executor.of(10);
        Executor jumboExecutor = Executor.of(200);
        System.out.println(regularExecutor.getName()); // RegularExecutor
        System.out.println(jumboExecutor.getName()); // JumboExecutor
    }
}
```

클라이언트는 이 두 클래스의 존재를 모른다. 이 중 하나의 클래스를 삭제해도 아무 문제가 없으며 새로운 클래스를 추가할 수도 있다.

### 5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.

이런 유연함은 서비스 제공자 프레임워크(Service Provider Framework)를 만드는 근간이 된다. 대표적인 서비스 제공자 프레임워크로는 JDBC(Java Database Connectivity)가 있다.

서비스 제공자 프레임워크는 3개의 핵심 컴포넌트로 이뤄진다.

1. 서비스 인터페이스(Service Interface) : 구현체의 동작을 정의한다.

2. 제공자 등록 API(Provider Registration API) : 제공자가 구현체를 등록할 때 사용한다.

3. 서비스 접근 API(Service Access API) : 클라이언트가 서비스 인스턴스를 얻을 때 사용한다.

3개의 핵심 컴포넌트와 더불어 종종 서비스 제공자 인터페이스(Service Provider Interface)라는 네 번째 컴포넌트가 쓰이기도 한다.

JDBC에서는 `Connection`이 서비스 인터페이스 역할을, `DriverManager.registreDriver`가 제공자 등록 API 역할을, `DriverManager.getConnection`이 서비스 접근 API 역할을, `Driver`가 서비스 제공자 인터페이스 역할을 수행한다.

## 정적 팩터리 메서드의 단점

### 1. 상속을 하려면 public이나 protected 생성자가 필요하니 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다.

컬렉션 프레임워크의 유틸리티 구현 클래스들은 상속할 수 없다.

이 제약은 상속보다 컴포지션을 사용하도록 유도하고 불변 타입으로 만든다는 점에서 오히려 장점으로 받아들일 수도 있따.

### 2. 정적 팩터리 메서드는 프로그래머가 찾기 어렵다.

생성자처럼 API 설명에 명확히 드러나지 않으니 사용자는 정적 팩토리 메서드 방식 클래스를 인스턴스화할 방법을 알아내야 한다. 이렇기 때문에 API 문서를 잘 써넣고 메서드 이름도 널리 알려진 규약을 따라 짓는 식으로 프로그래머가 찾기 쉽게 해야 한다.

다음은 정적 팩터리 메서드에 흔히 사용되는 명명 방식이다.

- from : 매개변수를 하나 받아 해당 타입의 인스턴스를 반환하는 형변환 메서드

```java
Date d = Date.from(instant);
```

- of : 여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메서드

```java
Set<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);
```

- valueOf : from과 of의 더 자세한 버전

```java
BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);
```

- intance 혹은 getInstance : (매개변수를 받는다면) 매개변수로 명시한 인스턴스를 반환하지만, 같은 인스턴스임을 보장하지는 않는다.

```java
StackWalker luke = StackWalker.getInstance(options);
```

- create 혹은 newInstance : instace 혹은 getInstance와 같지만, 매번 새로운 인스턴스를 생성해 반환함을 보장한다.

```java
Object newArray = Array.newInstance(classObject, arrayLen);
```

- getType : getInstace와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 쓴다.

```java
FileStore fs = Files.getFileStore(path);
```

- newType : newInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 쓴다.

```java
BufferedReader br = Files.newbufferedReader(path);
```

- type : getType과 newType의 간결한 버전

```java
List<Complaint> litany = Collections.list(legacyLitany);
```