# Strategy Pattern
- 전략 패턴은 유사한 행위들을 캡슐화하여, 객체의 행위를 바꾸고 싶은 경우 직접 변경이 아닌 전략만 변경하여 유연하게 확장하는 패턴이다.

## Strategy Pattern Structure

![Structure](https://upload.wikimedia.org/wikipedia/commons/3/39/Strategy_Pattern_in_UML.png)

- Strategy : 전략 사용을 위한 인터페이스

- ConcreteStrategyA, ConcreteStrategyB : Strategy 인터페이스의 전략을 구현한 클래스

- Context : 전략 인스턴스를 주입받아 직접 사용

## Use Strategy Pattern

- 다음 예제를 통해 전략 패턴에 대해 알아보자.

- 카페에서 커피를 선택하면 가격을 가져오는 전략이 있다고 가정하자.

### 커피의 가격을 가져오는 전략

- 커피 종류별로 `가격을 가져오다`라는 같은 행위를 가지고 있지만 서로 다른 가격을 가지고 있다.

- 공통적인 기능을 CoffeeStrategy 인터페이스에 정의한다.

```java
public interface CoffeeStrategy {
    int getPrice();
}
```

### 전략 메서드를 가진 전략 객체

- CoffeeStrategy 전략을 구현하는 클래스를 정의한다.

```java
public class AmericanoStrategy implements CoffeeStrategy {
     @Override
    public int getPrice() {
        return 3_000;
    }
}

public class CafeLatteStrategy implements CoffeeStrategy{
    @Override
    public int getPrice() {
        return 4_000;
    }
}
```

### 전략 객체를 사용하는 컨텍스트

- CoffeeStrategy 인터페이스 구현체에 따라 전략이 결정 되기 때문에 유연하게 전략을 변경할 수 있다.

```java
public class Coffee {

    private CoffeeStrategy coffeeStrategy;

    public void setCoffeeStrategy(CoffeeStrategy coffeeStrategy) {
        this.coffeeStrategy = coffeeStrategy;
    }

    public int getPrice() {
        return coffeeStrategy.getPrice();
    }
}
```

### 전략 객체 생성과 주입

- 다음과 같이 전략에 따라서 결과가 달라지는걸 확인할 수 있다.

```java
public static void main(String[] args) {
    Coffee coffee = new Coffee();

    CoffeeStrategy americano =  new AmericanoStrategy();
    coffee.setCoffeeStrategy(americano);
    System.out.println(coffee.getPrice()); // 3000

    coffee.setCoffeeStrategy(new CafeLatteStrategy(););
    System.out.println(coffee.getPrice()); // 4000
}
```

## Reference
- [전략 패턴](https://ko.wikipedia.org/wiki/%EC%A0%84%EB%9E%B5_%ED%8C%A8%ED%84%B4)