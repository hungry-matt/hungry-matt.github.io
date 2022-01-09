---
layout: default
title: item4-인스턴스화를 막으려거든 private 생성자를 사용하라
parent: Part1
grand_parent: Effective
---


Table of contents


- TOC
{:toc}


---

# 인스턴스화를 막으려거든 private 생성자를 사용하라

static 메서드와 static 필드만을 담은 유틸리티 클래스는 객체 지향과 거리가 멀어도 나름의 쓰임새가 있다. 하지만 추상 클래스로 만들게 되면 상속해서 인스턴스화를 할 수 있기 때문에 인스턴스화를 막을 수 없다.

기본적으로 생성자를 명시하지 않으면 컴파일러가 자동으로 public 생성자를 만들어 주기 때문에 private 생성자를 추가하여 클래스의 인스턴스화를 막을 수 있다.

## 정적 메서드와 정적 필드만을 담은 클래스

- java.lang.Math, java.util.Arrays
- java.util.Collections (특정 인터페이스를 구현하는 객체를 생성해주는 정적 메서드들의 모음)
- final 클래스와 관련한 메서드들의 모음

이와 같은 유틸리티 클래스들은 인스턴스로 만들어 쓰려고 설계된 게 아니다.
하지만 생성자를 명시하지 않으면 컴파일러가 기본 생성자를 만들어준다.

## private 생성자를 추가하여 인스턴스화 막기

추상 클래스로 만드는 것으로는 인스턴스화를 막을 수 없다.

- 하위 클래스를 만들어 인스턴스화하면 그만이다.
- 이를 본 사용자는 상속해서 쓰라는 뜻으로 오해할 수 있다.

다음 코드의 예는 기본 생성자가 만들어지는 것을 막는다.

```java
public class UtilityClass {
    private UtilityClass() {
        throw new AssertionError();
    }
}
```

- 명시적 생성자가 private이니 클래스 바깥에서는 접근할 수 없다.
    - 꼭 `Assertion Error`를 던질 필요는 없지만, 클래스 안에서 실수로라도 생성자를 호출하지 않도록 해준다.
- 이 방식은 상속을 불가능하게 하는 효과도 있다.
    - 모든 생성자는 상위 클래스의 생성자를 호출하게 되는데, private으로 선언했으니 하위 클래스가 상위 클래스의 생성자에 접근할 길이 막혀버린다.





