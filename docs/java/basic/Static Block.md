---
layout: default
title: Static Block
parent: Basic
grand_parent: Java
---

Table of contents

- TOC
{:toc}

---

# Static Block

- `static block`은 클래스가 메모리에 로드될 때 한 번만 실행된다.

- 클래스에 `static {...}` 형식으로 작성되며 주로 클래스 로드시 클래스 변수의 초기화나 예외 처리 등을 할 수 있다.

```java
public class StaticBlock {

    static StaticBlock INSTANCE = new StaticBlock();

    public StaticBlock() {
        System.out.println("Constructor called");
    }

    static {
        System.out.println("static block1 called");
    }

    static {
        System.out.println("static block2 called");
    }

    public static void print() {
        System.out.println("Method print called");
    }

}
```

- 클래스에는 여러 개의 static block이 있을 수 있고 작성 순서대로 호출되도록 보장되고 있다.

- static block은 클래스가 로드될 때 `인스턴스가 생성되기 전`과 `정적 메서드가 실행되기 전` 순서로 호출된다.

- 클래스 변수와 static block의 호출되는 순서를 알아보기 위해 main 메서드에서 호출해본다.

```java
public class Main {
    public static void main(String[] args) {
        StaticBlock.print();
        StaticBlock staticBlock1 = new StaticBlock();
        StaticBlock staticBlock2 = new StaticBlock();
    }
}
```

The console ouput :
```
Constructor called // class variable
static block1 called // static block
static block2 called // static block
Method print called // static method
Constructor called // constructor
Constructor called // constructor
```

- 클래스 변수는 클래스가 로드되고 초기화하기 때문에 static block 보다 먼저 호출이 되어 생성자의 문자가 출력되었다.

- static block은 개수와 상관없이 차례대로 호출되었고 한번 호출된 이후 정적 메서드 호출과 여러 개의 인스턴스를 생성하였지만 문자를 출력하지 않았다.

# Reference
- [Static blocks in Java](https://www.geeksforgeeks.org/g-fact-79/)