---
layout: default
title: item 7. 다 쓴 객체 참조를 해제하라
parent: Part1
grand_parent: Effective
---


Table of contents


- TOC
{:toc}


---

# 다 쓴 객체의 참조를 해제하라

메모리 누수는 겉으로 잘 드러나지 않아 시스템에 수년간 잠복하는 사례도 있다. 이런 누수는 철저한 코드 리뷰나 힙 프로파일러 같은 디버깅 도구를 동원해야만 발견되기도 한다. 그래서 이런 종류의 문제는 예방법을 익혀두는 것이 매우 중요하다.

## 가비지 컬렉터

C, C++ 처럼 메모리를 직접 관리해야 하는 언어를 쓰다가 자바처럼 가비지 컬렉터를 갖춘 언어로 넘어오면 프로그래머의 삶이 평안해진다. 다 쓴 객체를 알아서 회수해가니 말이다.

그래서 자칫 메모리 관리에 더 이상 신경 쓰지 않아도 된다고 오해할 수 있는데, 절대 사실이 아니다.

## 메모리 누수

다음 코드는 겉으로 봐서는 특별한 문제가 없어 보인다. 하지만 `메모리 누수`라는 문제가 숨어 있다.

이 스택을 사용하는 프로그램을 오래 실행하다 보면 점차 가비지 컬렉션 활동과 메모리 사용량이 늘어나 결국 성능이 저하될 것이다.

상대적으로 드문 경우긴 하지만 심할 때는 디스크 페이징이나 OutOfMemoryError를 일으켜 프로그램이 예기치 않게 종료되기도 한다.

```java
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public Object pop() {
        if (size == 0) {
            throw new EmptyStackException();
        }
        return elements[--size];
    }
 
    /**
     * 원소를 위한 공간을 적어도 하나 이상 확보한다.
     * 배열 크기를 늘려야 할 때마다 대략 두 배씩 늘린다.
     */
    private void ensureCapacity() {
        if (elements.length == size) {
            elements = Arrays.copyOf(elements, 2 * size + 1);
        }
    }
}
```

## 메모리 누수는 어디서 일어날까?

스택이 커졌다 줄어들었을 때 스택에서 꺼내진 객체들을 가비지 컬렉터가 회수하지 않는다. 이 스택이 그 객체들의 다 쓴 참조(obsolete reference)를 여전히 가지고 있기 때문이다.

여기서 다 쓴 참조란 문자 그대로 앞으로 다시 쓰지 않을 참조를 뜻한다. elements 배열의 '활성 영역' 밖의 참조들이 모두 여기에 해당하며 활성 영역은 인덱스가 size보다 작은 원소들로 구성된다.

## 찾기 까다로운 메모리 누수

객체 참조 하나를 살려두면(의도치 않게 객체를 살려두는) 가비지 컬렉터는 그 객체뿐 아니라 그 객체가 참조하는 모든 객체(그리고 또 그 객체들이 참조하는 모든 객체)를 회수해가지 못한다.

그래서 단 몇 개의 객체가 매우 많은 객체를 회수되지 못하게 할 수 있고 잠재적으로 성능에 악영향을 줄 수 있다.

## 메모리 누수의 해법

해법은 간단하다. 해당 참조를 다 썻을 때 null 처리(참조 해제)하면 된다.

```java
public Object pop() {
    if (size == 0) {
        throw new EmptyStackException();
    }
    Object result = elemetns[--size];
    elements[size] = null // 다 쓴 참조 해제;
    return result;
}
```

다 쓴 참조를 null 처리하면 다른 이점도 따라온다. 만약 null 처리한 참조를 실수로 사용하게 되면 즉시 NPE가 던져저 프로그램의 오류를 조기에 발견할 수 있다.

`객체 참조를 null 처리하는 일은 예외적인 경우여야 한다.` 다 쓴 참조를 해제하는 가장 좋은 방법은 그 참조를 담은 변수를 유효 범위(scope) 밖으로 밀어내는 것이다.

변수의 범위를 최소가 되게 정의했다면 이 일은 자연스럽게 이뤄진다.