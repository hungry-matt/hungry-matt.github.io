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
- 결국에는 `getInstance()` 정적 메서드를 통해 클래스 로딩 시 생성된 인스턴스를 반환 받을 수 있다.

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

## Static Block Initialization

- Eager Initialization과 유사 하지만 `static block`안에서 인스턴스를 생성하고 예외처리를 할 수 있다는 점이 있다.

```java
public class StaticBlockSingleton {

    private static StaticBlockSingleton instance;

    private StaticBlockSingleton() {

    }

    static {
        try {
            instance = new StaticBlockSingleton();
        } catch (Exception e) {
            throw new RuntimeException("Exception occured in creating singleton instance.");
        }
    }

    public static StaticBlockSingleton getInstance() {
        return instance;
    }
}

```

- static block은 클래스가 메모리에 로드될 때 처음 한번만 실행한다.
- Eager Initialization과 다르게 static block에서 인스턴스 생성 시 로직을 추가하거나 초기 변수 설정을 할 수 있다.
- 인스턴스가 사용되지 않아도 메모리에 로드되는 점은 다르지 않다.

## Lazy Initialization

- Lazy Initialization은 Eager Initialization의 단점이 보완된 싱글톤이다.

- 클래스 인스턴스가 사용되는 시점에 인스턴스가 생성되는 방식이다.

```java
public class LazySingleton {

    private static LazySingleton instance;

    private LazySingleton() {

    }

    public static LazySingleton getInstance() {
        if (instance == null) {
            instance = new LazySingleton();
        }

        return instance;
    }
}
```

- `getInstance()` 정적 메서드를 보면 내부에 if 문을 작성하였고 instance가 null인 경우에만 인스턴스를 생성하기 때문에 메모리에 부담이 적어졌다.

- 하지만 단일 스레드 환경에서는 잘 작동하지만 다중 스레드 환경일 경우 thread-safe 하지 않은 문제를 가지고 있다.

- instance가 아직 초기화 되지 않은 null 상태라고 가정하자.

- 여러 스레드가 getInstance() 메서드를 동시에 호출하면 if 문 내부에서는 instance가 null이기 때문에 각각의 스레드는 새로운 클래스 인스턴스를 생성하게 되어 싱글톤 패턴이 파괴되는 문제가 발생하게 된다.

## Thread-Safe Singleton

- Lazy Initialization가 가지고 있는 다중 스레드 문제를 해결한 싱글톤이다.

```java
public class ThreadSafeSingleton {

    private static ThreadSafeSingleton instance;

    private ThreadSafeSingleton() {

    }

    public static synchronized ThreadSafeSingleton getInstance() {
        if (instance == null) {
            instance = new ThreadSafeSingleton();
        }

        return instance;
    }
}
```

- `synchronized` 키워드를 이용하여 접근을 한 번에 하나의 스레드만 접근 하도록 하였다.

- 하지만 다중 스레드 환경에서 `getInstance()` 정적 메서드를 호출할 때 마다 동기화 비용이 발생한다.

- 싱글톤을 사용하는 목적은 하나의 인스턴스를 만드는 것이지 성능을 저하시키려는 것이 아니다.

## Bill Pugh Solution

- BillPughSingleton은 지금까지 봐왔던 싱글톤과 다르게 정적 내부 클래스를 가진다.

- 클래스 변수는 정적 내부 클래스에서 인스턴스화하며 `getInstance()` 메서드에 의해 호출시 생성이 된다.

```java
public class BillPughSingleton {

    private BillPughSingleton() {

    }

    private static class SingletonHelper {
        private static final BillPughSingleton INSTANCE = new BillPughSingleton();
    }

    public static BillPughSingleton getInstance() {
        return SingletonHelper.INSTANCE;
    }
}
```

- `getInstance()` 정적 메서드 호출시 BillPughSingleton 클래스가 로드된다.

- 메소드 내부의 SingletonHelper.INSTANCE도 static 이므로 SingletonHepler 클래스가 로드되며 인스턴스를 생성한다.

- Lazy Loading이 가능하며 호출되기 전에는 클래스를 로드하지 않기 때문에 메모리의 부담이 적다.

- 클래스가 로드될때 인스턴스를 생성하기 때문에 동기화가 필요하지 않아 다중 스레드 환경에서 thread-safe 하다.

## Using Reflection to Destroy Singleton Patterns

- Bill Pugh Solution을 마지막으로 클래스 기반 싱클톤의 단점을 보완하는 과정들을 살펴봤지만 여전히 해결되지 않은 문제가 남아있다.

- 그것은 Relection에 의해 하나 이상의 인스턴스가 생성될 수 있다는 것이다.

```java
public static void main(String[] args) {
    BillPughSingleton instance1 =  BillPughSingleton.getInstance();
    BillPughSingleton instance2 = null;

    Constructor[] constructors = instance1.getClass().getDeclaredConstructors();

    try {
        for (Constructor constructor : constructors) {
            constructor.setAccessible(true);
            instance2 = (BillPughSingleton) constructor.newInstance();
            break;
        }
    } catch (Exception e) {
        e.printStackTrace();
    }

    System.out.println(instance1.hashCode()); //460141958
    System.out.println(instance2.hashCode()); //1163157884
}
```
- Reflection은 클래스의 생성자를 검사하고 런타임중 객체를 인스턴스화할 수 있다.

- `getDeclaredConstructors()` 메소드에 의해 인스턴스화된 클래스의 모든 생정자를 가져온다.

- `setAccessible(true)` 메서드는 모든 private 생성자, 메서드에 접근할 수 있게 해준다.

- 접근이 허용되면 `newInstance()` 메서드에 의해 인스턴스를 초기화할 수 있다.

- 출력 결과를 통해 싱글톤 패턴이 파괴된 것을 확인할 수 있다.

# 2. Enum Singleton

- Enum Singleton은 Reflection을 통해 패턴이 파괴되는걸 막기 위해 사용한다.

- Java에서는 모든 enum값이 한 번만 인스턴스화 되도록 보장하고 있기 때문에 싱글톤 패턴을 구현하는데 적합하다.

- 다중 스레드 환경에서도 thread-safe 하도록 보장받고 있다. (그렇지만 사용자가 구현한 메서드는 보장받지 못한다.)

- 직렬화/역직렬화에 대한 별도의 처리가 필요하지 않다.

```java
public enum EnumSingleton {
    INSTANCE;
    public void someMethod() {

    }
}
```

## Serialization and Singleton

- 때때로 파일시스템을 통해 상태를 저장하거나 검색할 수 있도록 클래스를 직렬화해야 할 경우가 있다.

- 다음은 Serialization을 구현한 Singleton이다.

```java
public class SerializationSingleton implements Serializable {

    private SerializationSingleton() {

    }

    public static SerializationSingleton getInstance() {
        return SerializationSingletonHelper.INSTANCE;
    }

    public static class SerializationSingletonHelper {
        private static final SerializationSingleton INSTANCE = new SerializationSingleton();
    }
}
```

- 직렬화된 싱글톤 클래스는 역직렬화시 새로운 인스턴스가 생성되는 문제가 있다.

- 다음 예제의 main 메서드에 직렬화, 역직렬화를 구현하였다.

```java
public static void main(String[] args) {
    SerializationSingleton instance1 = SerializationSingleton.getInstance();

    try(ObjectOutput output = new ObjectOutputStream(new FileOutputStream("filename.txt"));) {
        output.writeObject(instance1);
    } catch (Exception e) {
        e.printStackTrace();
    }

    SerializationSingleton instance2 = null;

    try(ObjectInput input = new ObjectInputStream(new FileInputStream("filename.txt"))) {
        instance2 = (SerializationSingleton) input.readObject();
    } catch (Exception e) {
        e.printStackTrace();
    }

    System.out.println(instance1.hashCode()); // 929338653
    System.out.println(instance2.hashCode()); // 824909230
}
```
- 결과를 보면 두 인스턴스를 서로 다른 hashCode를 가진걸 확인할 수 있다.

- Serializable 인터페이스를 구현한 클래스는 역직렬화할 때 readObject()를 호출하면서 새로운 인스턴스를 만든다.

- 새로운 인스턴스가 생성되지 않기 위해서는 다음과 같이 `readResolve()` 메서드를 구현하면 된다.

```java
protected Object readResolve() {
    return getInstance();
}
```

- 다음은 위 상황을 Enum 싱글톤 패턴으로 구현하였다.

```java
public static void main(String[] args) {
    EnumSingleton instance1 = EnumSingleton.INSTANCE;

    try(ObjectOutput out = new ObjectOutputStream(new FileOutputStream("filename.txt"));) {
        out.writeObject(instance1);
    } catch (Exception e) {
        e.printStackTrace();
    }

    EnumSingleton instance2 = null;

    try(ObjectInput input = new ObjectInputStream(new FileInputStream("filename.txt"))) {
        instance2 = (EnumSingleton) input.readObject();
    } catch (Exception e) {
        e.printStackTrace();
    }

    System.out.println(instance1.hashCode()); // 1735600054
    System.out.println(instance2.hashCode()); // 1735600054
}
```

- 클래스 기반 싱글톤과 다르게 역직렬화에 대한 별도의 처리를 해주지 않아도 같은 hashCode가 출력되었다.

- 지금까지 여러 상황을 고려하지 않고도 기본적인 Enum 클래스로 간단히 싱글턴 패턴을 구현할 수 있는걸 확인할 수 있었다.

# Reference
- [싱글턴 패턴](https://ko.wikipedia.org/wiki/%EC%8B%B1%EA%B8%80%ED%84%B4_%ED%8C%A8%ED%84%B4)
- [Singletons in Java](https://www.baeldung.com/java-singleton)
- [What is an efficient way to implement a singleton pattern in Java?](https://stackoverflow.com/questions/70689/what-is-an-efficient-way-to-implement-a-singleton-pattern-in-java)
- [All About the Singleton](https://dzone.com/articles/all-about-the-singleton)