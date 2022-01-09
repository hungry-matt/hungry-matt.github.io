---
layout: default
title: item6-불필요한 객체 생성을 피하라
parent: Part1
grand_parent: Effective
---


Table of contents


- TOC
{:toc}


---

# 불필요한 객체 생성을 피하라

똑같은 기능의 객체를 매번 생성하기보다는 객체 하나를 재사용하는 편이 나을 때가 많다.

## 문자열 객체 생성

```java
String s = new String("bikini"); // 따라 하지 말 것!
```

위 문장은 실행될 때마다 String 인스턴스를 새로 만들기 때문에 반복문이나 빈번히 호출되는 메서드가 있다면 불필요한 String 인스턴스가 수백만 개가 만들어질 수도 있다.

```java
String s = "bikini";
```

이 코드는 새로운 인스턴스를 매번 만드는 대신 하나의 String 인스턴스를 재사용한다. 이 방식을 사용한다면 같은 가상 머신 안에서 이와 똑같은 문자열 리터럴을 사용하는 모든 코드가 같은 객체를 재사용함이 보장된다.

## 정적 팩터리 메서드 사용

생성자 대신 정적 팩터리 메서드를 제공하는 불변 클래스에서는 정적 팩터리 메서드를 사용해 불필요한 객체 생성을 피할 수 있다. 

```java
Boolean b = new Boolean("true");
Boolean b1 = Boolean.valueOf("true");
Boolean b2 = Boolean.valueOf("true");

System.out.println(b == b1) // false
System.out.println(b1 == b2) // true
```

생성자는 호출 할 때마다 새로운 객체를 만들지만, 팩터리 메서드는 전혀 그렇지 않다.

## 생성 비용이 비싼 객체

생성 비용이 아주 비싼 객체도 더러 있다. 이런 '비싼 객체'가 반복해서 필요하다면 캐싱하여 재사용하길 권한다.

```java
public static boolean isRomanNumeral(String s) {
    return s.matches("^(?=.)M*(C[MD]|D?C{0,3})"
            + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
}
```

이 방식의 문제는 String.matches 메서드를 사용하는 데 있다. `String.matches`는 정규표현식으로 문자열 형태를 확인하는 가장 쉬운 방법이지만, 성능이 중요한 상황에서 반복해 사용하기엔 적합하지 않다. 

이 메서드 내부에는 정규표현식용 Pattern 인스턴스는 한 번 쓰고 버려져서 곧바로 가비지 컬렉션 대상이 된다. Pattern은 입력받은 정규표현식에 해당하는 유한 상태 머신을 만들기 때문에 인스턴스 생성 비용이 높다.

성능을 개선하려면 필요한 정규표현식을 표현하는 Pattern 인스턴스를 클래스 초기화(정적 초기화) 과정에서 직접 생성해 캐싱해두고, 나중에 isRomanNumeral 메서드가 호출될 때마다 이 인스턴스를 재사용한다.

```java
public class RomanNumerals {
    private static final Pattern ROMAN = Pattern.compile("^(?=.)M*(C[MD]|D?C{0,3})"
            + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");

    public static boolean isRomanNumeral(String s) {
        return ROMAN.matcher(s).matches();
    }
}
```

이렇게 개선된 메서드는 빈번히 호출되는 상황에서 성능을 상당히 끌어올릴 수 있다.

## 어댑터

객체가 불변이라면 재사용해도 안전함이 명백하다. 하지만 훨씬 덜 명확하거나, 심지어 직관에 반대되는 상황도 있다. 

예를들어 어댑터는 실제 작업은 뒷단 객체에 위임하고 뒷단 객체만 관리하면 된다. 즉, 뒷단 객체 외에는 관리할 상태가 없으므로 뒷단 객체 하나당 어댑터 하나씩만 만들어지면 충분하기에 여러개의 인스턴스를 만들 필요가 없다.

```java
Map<String, String> map = new HashMap<>();
map.put("String", "String");

Set<String> set1 = map.keySet();
Set<String> set2 = map.keySet();

assertSame(set1, set2); // ture

set1.remove("String");
System.out.println(set1.size()); // 0
System.out.println(set2.size()); // 0
```

Map 인터페이스의 keySet 메서드는 Map 객체 안의 키 전부를 담은 Set 뷰를 반환한다. keySet을 호출할 때마다 새로운 Set 인스턴스가 만들어지는게 아니기 때문에 반환한 객체 중 하나를 수정하면 다른 모든 객체가 따라서 바뀐다. 즉, 모두가 똑같은 Map 인스턴스를 대변하기 때문에 keySet이 뷰 객체를 여러개 만들 필요가 없다.

## 오토박싱

불필요한 객체를 만들어내는 또 다른 예로 오토박싱이 있다.

```java
public static long sum() {
    Long sum = 0L;
    for (long i = 0; i <= Integer.MAX_VALUE; i++) {
        sum += i;
    }
    return sum;
}
```

위 코드의 문제는 반복문안에서 더하기 연산을 할 때마다 `long -> Long` 타입으로 변환하는 작업 때문에 훨씬 느린 속도로 처리가 된다. sum 변수를 long 타입으로 바꿔주면 내 컴퓨터에서는 7초에서 0.9초로 빨라진다.

`박싱된 기본 타입보다는 기본 타입을 사용하고, 의도치 않은 오토박싱이 숨어들지 않도록 주의하자.`

## 오해 금지

이번 아이템을 "객체 생성은 비싸니 피해야 한다"로 오해하면 안된다.

특히나 요즘의 JVM에서는 별다른 일을 하지 않는 작은 객체를 생성하고 회수하는 일일 크게 부담되지 않는다. 프로그램의 명확성, 간결성, 기능을 위해서 객체를 추가로 생성하는 것이라면 일반적으로 좋은 일이다.

거꾸로, 단순히 객체 생성을 피하고자 여러분만의 객체 풀(pool)을 만들지는 말자. 데이터베이스 연결 같은 경우 생성 비용이 워낙 비싸니 재사용하는 편이 낫다. 하지만 일반적으로는 자체 객체풀은 코드를 헷갈리게 만들고 메모리 사용량을 늘려 성능을 떨어뜨린다. 

요즘 JVM의 가비지 컬렉터는 상당이 잘 최적화되어서 가벼운 객체를 다둘 때는 직접 만든 객체 풀보다 훨씬 빠르다.

## 재사용 vs 방어적 복사

이번 아이템은 "기존 객체를 재사용해야 한다면 새로운 객체를 만들지 마라"라면, 아이템 50은 "새로운 객체를 만들어야 한다면 기존 객체를 재사용하지 마라"다. 

방어적 복사가 필요한 상황에서 객체를 재사용했을 때의 피해가, 필요 없는 객체를 반복 생성했을 때의 피해보다 훨씬 크다는 사실을 기억하자.

서로 대조적이지만 상황에 따라 객체를 재사용할지 방어적 복사를 통해 객체를 재사용하지 않을지에 대해 잘 판단하는 것이 중요하다.