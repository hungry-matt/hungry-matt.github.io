---
layout: default
title: item5-자원을 직접 명시하지 않고 의존 객체 주입을 사용하라
parent: Part1
grand_parent: Effective
---


Table of contents


- TOC
{:toc}


---

# 자원을 직접 명시하지 않고 의존 객체 주입을 사용하라

## 핵심정리

클래스가 내부적으로 하나 이상의 자원에 의존하고, 그 자원이 클래스 동작에 영향을 준다면 `싱글턴`과 `유틸리티 클래스`는 사용하지 않는 것이 좋다. 이 자원들을 클래스가 직접 만들게 해서도 안 된다. 대신 필요한 자원을 (혹은 그 자원을 만들어주는 팩터리를) 생성자에 (혹은 정적 팩터리나 빌더에) 넘겨주자. 의존 객체 주입이라 하는 이 기법은 클래스의 `유연성, 재사용성, 테스트 용이성`을 기막히게 개선해준다.

## 적합하지 않은 방식

많은 클래스가 하나 이상의 자원에 의존한다. 다음 코드는 정적 유틸리티와 싱글턴을 잘못 사용하는 예이다.


```java
// 정적 유틸리티를 잘못 사용한 예
public class SpellChecker {
    private static final Lexcion dictionary = ...;

    private SpellChecker() {} // 객체 생성 방지

    public static boolean isValid(String word) {
        ...
    }

    public static List<String> suggestions(String typo) {
        ...
    }
}

// 싱글턴을 잘못 사용한 예
public class SpellChecker {
    private static Lexcion dictionary = ...;

    private SpellChecker(...) {
       ... 
    }

    public static SpellChecker INSTANCE = new SpellChecker(...);

    public static boolean isValid(String word) {
        ...
    }

    public static List<String> suggestions(String typo) {
        ...
    }
}
```

위의 두 방식 모두 유연하지 않고 테스트하기 어려우며 `사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식은 적합하지 않다.`

클래스(SpellChecker)가 여러 자원 인스턴스를 지원해야 하며, 클라이언트가 원하는 자원(dictionary)을 사용해야 한다면 `인스턴스를 생성할 때 생정자에 필요한 자원을 넘겨주는 방식`을 사용하면 된다.

이는 의존 객체 주입의 한 형태로, 맞춤법 검사기를 생성할 때 의존 객체인 사전을 주입해주면 된다.

```java
public class SpellChecker {
    private final Lexcion dictionary;

    public SpellChecker(Lexcion dictionary) {
        this.dictionary = Objects.requiredNonNull(dictionary);
    }

    public boolean isValid(String word) {
        ...
    }

    public List<String> suggestions(String typo) {
        ...
    }
}
```

의존 객체 주입 패턴은 아주 단순하며 불변을 보장하여 (같은 자원을사용하려는) 여러 클라이언트가 의존 객체들을 안심하고 공유할 수 있기도 하다.