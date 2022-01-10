---
layout: default
title: item 9. try-finally 보다는 try-with-resource를 사용하라.
parent: Part1
grand_parent: Effective
---


Table of contents


- TOC
{:toc}


---

# try-finally 보다는 try-with-resource를 사용하라.

- 자바 라이브러리에는 close 메서드를 호출하여 자원을 닫아줘야 하는 자원들(InputStream, OutpurtStream, java.sql.Connection 등이 해당)이 많다.

- 이런 자원들을 사용하고 반환하는걸 놓치게 되면 성능 문제로 이어지기도 한다. 

## 전통적인 자원 회수 방법

```java
static String firstLineOfFile(String path) throws IOException {
    BufferedReader br = new BufferedReader(new FileReader(path));
    try {
        retrun br.readLine();
    } finally {
        br.clsoe();
    }
}
```

## 전통적인 자원 회수 방법은 가독성이 떨어진다

- try-finally 블럭이 추가될 때 마다 알아보기 힘들어진다.

- 가독성뿐만 아니라 예를 들어 아래 코드 1번에서 예외가 발생하여 던지게 되면 2번에서도 같은 이유로 예외를 던지게 될 것이다.

- 이렇게 되면 두 번째 예외가 첫 번째 예외를 덮어버리게 되며 실제 예외가 발생한 정보를 알기 어려워진다.

```java
static void copy(String src, String dst) throws IOException {
    InputStream in = new FileInputStream(src);
    try {
        OutputStream out = new FileOutputStream(dst);
        try {
            byte[] buf = new byte[BUFFER_SIZE];
            int n;
            while ((n = in.read(buf)) >= 0) {
                out.write(buf, 0, n); // 1
            }
        } finally {
            out.close(); // 2
        }
    } finally {
        in.close();
    }
}
```

## 자바 7부터 개선된 구조

- try-with-resource를 사용하려면 AutoCloseable 인터페이스의 close 메서드를 구현해야 한다.

- try-finally 블록을 중첩해서 사용했을 때 보다 훨씬 깔끔하게 사용이 가능하다.

- 자원 반환을 직접 하지 않아도 된다.

```java
static void copy(String src, String dst) throws IOException {
    try (InputStream in = new FileInputStream(src);
         OutputStream out = new FileOutputStream(dst)) {
            byte[] buf = new byte[BUFFER_SZIE];
            int n;
            while ((n = in.read(buf)) >= 0) {
                out.write(buf, 0, n);
            }
    }
}
```