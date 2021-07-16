---
layout: default
title: Singleton Pattern
parent: Design Pattern
grand_parent: Java
---

Table of contents

- TOC
{:toc}

----

# Singleton Pattern
- 전체 애플리케이션내에서 단 하나의 인스턴스를 생성하기 위한 방법이다.
- 주로 서로 자원을 공유 할 때 사용되며 스프링 프레임워크에서는 빈을 싱글톤으로 관리하고 있다.
- 구현에는 다양한 종류가 있는데, 크게 두 가지 방법으로 나누어 보겠다.

# 1. Class-Based Singleton

- 가장 일반적으로 사용되는 방식으로 클래스를 만들고 Singleton을 구현하는 방법이다.
- 기본적으로 3가지 요소로 구성되어 있다.
    1. private 생성자
    2. 유일한 instance를 포함하는 정적 필드
    3. instance를 반환 하는 정적 팩토리 메서드

    ![Singleton](/images/design-pattern/singleton.jpg)

- 많이 사용되는 방식이지만 클래스 기반의 싱글톤은 몇가지 문제점을 가지고 있다.
    - 하나 이상의 인스턴스가 생성될 수 있다.
    - 멀티쓰레드 환경에서는 thread-safe 하지 않다.
- 다음을 통해 클래스 기반의 다양한 싱글톤 생성과 문제점에 대해 알아보겠다.

## Eager Initialization

- EagerSingleton은 클래스 로딩 시 인스턴스가 생성되는 방식이다.

- 가장 간단한 방식이지만 애플리케이션에서 사용되지 않더라도 인스턴스가 생성되어 메모리에 로드되는 단점이 있다.

```java
public class EagerSingleton {
    private static volatile EagerSingleton instance = new EagerSingleton();

    // private constructor
    private EagerSingleton() {
    }

    public static EagerSingleton getInstance() {
        return instance;
    }
}
```

- 먼저 전역 변수인 instance에 인스턴스 생성을 한다.
- `static` 키워드가 붙은 클래스 변수는 인스턴스화와 상관없이 사용이 가능하다.
- 하지만 `private` 접근제어자로 직접 접근이 불가능한 상태이다.
- 생성자에도 `private` 키워드를 명시하여 외부에서 `new` 키워드로 인스턴스화 하지 못하게 제한하였다.
- 결국에는 `getInstance()` 메서드를 통해 클래스 로딩 시 생성된 인스턴스를 반환 받을 수 있다.

### 클래스 로딩 확인해보기

- Eager Initialization의 싱글톤 생성의 단점인 인스턴스가 항상 생성되는지 간단히 확인해보자.

- EagerSingleton에 `doNoting()` 정적 메서드와 비교를 위한 Test 클래스를 만들어 main 메서드에서 호출해보겠다.

- 호출에 앞서 클래스가 로드되는걸 확인하기 위해 JVM `verbose:class` 옵션을 추가한다.

```java
//EagerSingleton class
public static void doNoting() {

}

//Test class
public class Test {
    public void loaded() {
        System.out.println("loaded!");
    }
}

//Main class
public static void main(String[] args) {
    EagerSingleton.doNoting();
    
    new Test().loaded();
}
```

The console output :

```
[Loaded com.design.singleton.EagerSingleton from file:/C:/Users/Documents/spring-boot-getting-started/out/production/design/]
[Loaded com.design.singleton.Test from file:/C:/Users/Documents/spring-boot-getting-started/out/production/design/]
loaded!!!
```

- 출력 내용을 보면 Test 클래스를 인스턴스화 하여 로드된 것과 main 메서드에서 인스턴스화를 하지 않은 EagerSingleton 클래스가 로드된 것을 확인할 수 있다.

- 위 내용에서 살펴본 것 처럼 `getInstance()` 메서드를 호출 하지 않고도 `static` 키워드를 통해 클래스는 항상 로드되고 정적 변수에 인스턴스가 생성 되는 단점에 대해 알아보았다.

# 2. Enum Singleton

# Reference
- [싱글턴 패턴](https://ko.wikipedia.org/wiki/%EC%8B%B1%EA%B8%80%ED%84%B4_%ED%8C%A8%ED%84%B4)
- [Singletons in Java](https://www.baeldung.com/java-singleton)
- [What is an efficient way to implement a singleton pattern in Java?](https://stackoverflow.com/questions/70689/what-is-an-efficient-way-to-implement-a-singleton-pattern-in-java)
- [All About the Singleton](https://dzone.com/articles/all-about-the-singleton)