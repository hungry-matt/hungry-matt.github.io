---
layout: default
title: item 2. 생성자에 매개변수가 많다면 빌더를 고려하라
parent: Part1
grand_parent: Effective
---


Table of contents


- TOC
{:toc}


---

# 생성자에 매개변수가 많다면 빌더를 고려하라

## 핵심 정리
```
생성자나 정적 팩터리가 처리해야 할 매개변수가 많다면 빌더 패턴을 선택하는게 더 낫다. 매개변수 중 다수가 필수가 아니거나 같은 타입이면 특히 더 그렇다. 빌더는 점층적 생성자보다 클라이언트 코드를 읽고 쓰기가 훨씬 간결하고, 자바빈즈보다 훨씬 안전하다.
```

정적 팩터리와 생성자에는 똑같은 제약이 하나 있다. 선택전 매개변수가 많을 때 적절히 대응하기 어렵다는 점이다.

다음 패턴들을 통해 어떤 문제가 있는지 알아보자.

## 점층적 생성자 패턴

그동안 프로그래머들은 점층적 생성자 패턴을 즐겨 사용해 왔다. 필수 매개변수만 받는 생성자, 필수 매개변수와 선택 매개변수를 1개 받는 생성자... 등의 형태로 선택적 매개변수를 받는 생성자를 늘려가는 방식이다. 다음 코드가 그 예다.

```java
public class NutritionFacts {
    private final int servingSize; // m1, 1회 제공량 (필수)
    private final int servings; // 회, 총 n회 제공량 (필수)
    private final int calories; // 1회 제공량당 (선택)
    private final int fat; // g/1회 제공량 (선택)
    private final int sodium; // mg/1회 제공량 (선택)
    private final int carbohydrate; // g/1회 제공량 (선택)

    public NutritionFacts(int servingSize, int servings) {
        this(servingSize, servings, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories) {
        this(servingSize, servings, calories, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat) {
        this(servingSize, servings, calories, fat, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium) {
        this(servingSize, servings, calories, fat, sodium, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate) {
        this.servingSize = servingSize;
        this.servings = servings;
        this.calories = calories;
        this.fat = fat;
        this.sodium = sodium;
        this.carbohydrate = carbohydrate;
    }
}

NutritionFacts cocaCola = new NutritionFacts(240, 8, 100, 0, 35, 27);
```

이런 생성자는 사용자가 설정하길 원치 않는 매개변수까지 포함하기 쉬운데, 어쩔 수 없이 그런 매개변수의 값도 지정해줘야 한다.

점층적 생성자 패턴은 `매개변수 개수가 많아지면 클라이언트 코드를 작성하거나 읽기가 어렵다.` 각 매개변수가 의미가 무엇인지 알기 쉽지 않고 매개변수의 순서를 잘못 알고 값을 넣을 경우 엉뚱한 생성자가 만들어지게 된다.

## 자바빈즈 패턴

이번에는 선택 매개변수가 많을 때 활용할 수 있는 자바빈즈 패턴에 대해 알아보자. 매개변수가 없는 생성자를 만들고 setter 메서드들을 호출해 원하는 매개변수의 값을 설정하는 방식이다.

```java
public class NutritionFacts {
    // 매개변수들은 (기본값이 있다면) 기본값으로 초기화된다.
    private int servingSize = -1; // 필수; 기본값 없음
    private int servings = -1; // 필수; 기본값 없음
    private int calories = 0;
    private int fat = 0;
    private int sodium = 0;
    private int carbohydrate = 0;

    public NutritionFacts() {
    }

    public void setServingSize(int servingSize) {
        this.servingSize = servingSize;
    }

    public void setServings(int servings) {
        this.servings = servings;
    }

    public void setCalories(int calories) {
        this.calories = calories;
    }

    public void setFat(int fat) {
        this.fat = fat;
    }

    public void setSodium(int sodium) {
        this.sodium = sodium;
    }

    public void setCarbohydrate(int carbohydrate) {
        this.carbohydrate = carbohydrate;
    }
}
```

점층적 생성자 패턴의 단점들이 자바빈즈 패턴에서는 더 이상 보이지 않는다. 코드가 길어지긴 했지만 인스턴스를 만들기 쉽고, 그 결과 더 읽기 쉬운 코드가 되었다.

```java
NutritionFacts cocaCola = new NutritionFacts();

cocaCola.setServingSize(240);
        cocaCola.setServings(8);
        cocaCola.setCalories(100);
        cocaCola.setSodium(35);
        cocaCola.setCarbohydrate(27);
```

하지만 객체 하나를 만드려면 메서드를 여러 개 호출해야 하고, 객체가 완전히 생성되기 전까지 일관성이 무너져 클래스를 불변으로 만들 수 없는 단점이 있다.

## 빌더 패턴

빌더 패턴은 앞에서 알아본 점층적 생성자 패턴과 자바빈즈 패턴의 장점만을 취하였다. 클라이언트는 필요한 객체를 직접 만드는 대신, 필수 매개변수만으로 생성자(혹은 정적 팩터리)를 호출해 빌더 객체를 얻는다. 그런 다음 빌더 객체가 제공하는 setter 메서드들로 원하는 선택 매개변수를 설정한다. 마지막으로 매개변수가 없는 build 메서드를 호출해 불변 객체를 얻는다. 다음 코드가 그 예다.

```java
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public static class Builder {
        // 필수 매개변수
        private final int servingSize;
        private final int servings;

        // 선택 매개변수 - 기본값으로 초기화한다.
        private int calories = 0;
        private int fat = 0;
        private int sodium = 0;
        private int carbohydrate = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings = servings;
        }

        public Builder calories(int val) {
            calories = val;
            return this;
        }

        public Builder fat(int val) {
            fat = val;
            return this;
        }

        public Builder sodium(int val) {
            sodium = val;
            return this;
        }

        public Builder carbohydrate(int val) {
            carbohydrate = val;
            return this;
        }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }

    private NutritionFacts(Builder builder) {
        servingSize = builder.servingSize;
        servings = builder.servings;
        calories = builder.calories;
        fat = builder.fat;
        sodium = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }
}

NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
                    .calories(100)
                    .sodium(35)
                    .carbohydrate(27)
                    .build();
```

NutritionFacts 클래스는 불변이며, 빌더의 세터 메서드들은 빌더 자신을 반환하기 때문에 연쇄적으로 호출 할 수 있다. 이런 방식을 메서드 호출이 흐르듯 연결된다는 뜻으로 플루언트 API 혹은 메서드 연쇄라 한다. 빌더 패턴은 쓰기 쉽고, 무엇보다도 읽기가 쉽다는 장점을 가지고 있다.