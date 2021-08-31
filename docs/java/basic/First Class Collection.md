---
layout: default
title: First Class Collection
parent: Basic
grand_parent: Java
---

# 일급 컬렉션

- 일급 컬렉션은 컬렉션 외에 반드시 다른 멤버 변수가 없는 클래스를 의미한다.

## 일급 컬렉션의 이점

### 비지니스에 종속적인 자료구조

다음은 일반적인 서비스 메서드 코드이다.

```java
public class LottoService {
    private static final int LOTTO_NUMBERS_SIZE = 6;

    public void createLottoNumber() {
        List<Long> lottoNumbers = createNonDuplicateNumbers();
        validateSize(lottoNumbers);
        validateDuplicate(lottoNumbers);
    }

    private void validateSize(Lis<Long> lottoNumbers) {
        if (lottoNumbers.size() != LOTTO_NUMBERS_SIZE) {
            throw new IllegalArgumentException("로또 번호는 6개만 가능합니다.");
        }
    }

    private void validateDuplicate(List<Long> lottoNumbers) {
        Set<Long> nonDuplicateNumbers = new HashSet<>(lottoNumbers);
        if (nonDuplicateNumbers.size() != LOTTO_NUMBERS_SIZE) {
            throw new IllegalArgumentException("로또 번호는 중복될 수 없습니다.");
        }
    }

}
```
이와 같은 로직의 문제는 로또번호를 사용하는 로직에 똑같이 검증 로직이 들어가야 한다는 것이다.

그래서 다음과 같은 검증 로직이 들어간 자료구조를 만들게 되면 위의 문제를 해결할 수 있다.

```java
public class LottoTicket {
    private static final int LOTTO_NUMBERS_SIZE = 6;

    private final List<Long> lottoNumbers;

    public LottoTicket(List<Long> lottoNumbers) {
        validateSize(lottoNumbers);
        validateDuplicate(lottoNumbers);
        this.lottoNumbers = lottoNumbers;
    }

    private void validateSize(List<Long> lottoNumbers) {
        if (lottoNumbers.size() != LOTTO_NUMBERS_SIZE) {
            throw new IllegalArgumnetException("로또 번호는 6개만 가능합니다.");
        }
    }

    private void validateDuplicate(List<Long> lottoNumbers) {
        Set<Long> nonDuplicateNumbers = new HashSet<>(lottoNumbers);
        if (nonDuplicateNumbers.size() != LOTTO_NUMBERS_SIZE) {
            throw new IllegalArgumentException("로또 번호는 중복될 수 없습니다.");
        }
    }
}
```

```java
public class LottoService {
    public void createLottoNumber() {
        LottoTicket lottoTicket = new LottoTicket(createNonDuplicateNumbers());
    }
}
```

로또 번호 생성에 필요한 검증 로직들은 전부 LottoTicket에서 구현되어 이후 문제가 발생되어도 최소화할 수 있게 되었다.

### 컬렉션의 불변

일급 컬렉션은 `컬렉션의 불변`을 보장 한다.

final 키워드 사용은 재할당만 금지될 뿐 컬렉션 내부의 값은 변경이 가능하다.

소프웨어의 규모가 커질수록 불변 객체의 필요성은 더욱 높아진다.

각각의 객체들의 값이 유알하게 보장된다면 사이드 이펙트가 최소화되기 때문이다.

```java
public class Orders {
    private final List<Order> orders;

    public Orders(List<Order> orders) {
        this.orders = orders;
    }

    public long getAmountSum() {
        return orders.stream()
                .mapToLong(Order::getAmout)
                .sum();
    }
}
```

위와 같이 컬렉션의 값을 변경할 수 있는 메서드가 없는 컬렉션을 불변 컬렉션이라 한다.

Orders 클래스는 생성자와 getAmoutSum() 메서드를 통해서 새로 만들거나 값을 반환할 수 밖에 없다.

이 처럼 컬렉션의 값을 변경하거나 추가하지 않는것을 일급 컬렉션이라 한다.

### 상태와 행위를 한곳에서 관리

일급 컬렉션은 `값과 로직이 함께`한다는 이점이 있다.

일반적으로 컬렉션을 사용하게 되면 다음과 같이 작성하게 된다.

```java
@Test
void 로직이_밖에_있는_상태() {
    List<Pay> pays = Arrays.asList(
        new Pay(NAVER_PAY, 1000),
        new Pay(NAVER_PAY, 2000),
        new Pay(NAVER_PAY, 3000),
        new Pay(KAKAO_PAY, 1000L));

    Long naverPaySum = pays.stream()
                .filter(pay -> pay.getPayType.equals(NAVER_PAY))
                .mapToLong(Pay::getAmount)
                .sum();

    assertEquals(naverPaySum, 6000);
}
```

위와 같이 컬렉션과 계산로직은 서로 관계가 있는데 여기서 문제가 있다.

먼저 다른 곳에서 컬렉션을 사용하게 된다면 계산 로직이 있는것을 모르고 누락이 되거나 중복된 계산 로직이 생길 수 있다.

또한 PayType이 새로 생길때 마다 다른 계산 로직이 생기게 된다면 점차 관리 포인트가 늘어나게 된다.

그래서 이런 문제도 일급 컬렉션으로 해결할 수 있다.

```java
pulbic class PayGroups {
    private List<Pay> pays;

    public PayGroups(List<Pay> pays) {
        this.pays = pays;
    }

    public Long getNaverPaySum() {
        return pays.stream()
                .filter(pay -> PayType.isNaverPay(pay.getPayType()))
                .mapToLong(Pay::getAmount())
                .sum();
    }
}
```

각가의 타입별 계산식이 필요하다면 다음과 같이 람다식으로 리팩토링할 수 있다.

```java
public class PayGroups {
    private List<Pay> pays;

    public PayGroups(List<Pay> pays) {
        this.pays = pays;
    }

    public Long getNaverPaySum() {
        return getFilteredPays(pay -> PayType.isNaverPay(pay.getPayType()));
    }

    public Long getKakaoPaySum() {
        return getFilteredPays(pay -> PayType.isKakaoPay(pay.getPayType()));
    }

    public Long getFilteredPays(Pradicate<Pay> predicate) {
        return pays.stream()
                .filter(predicate)
                .mapToLong(Pay::getAmount())
                .sum();
    }
}
```

처음 보았던 컬렉션과 계산로직을 다음과 같이 일급 컬렉션을 통해 한곳에서 관리할 수 있게 되었다.

```java
@Test
void 로직과_값이_한곳에() {
    List<Pay> pays = Arrays.asList(
        new Pay(NAVER_PAY, 1000),
        new Pay(NAVER_PAY, 2000),
        new Pay(KAKAO_PAY, 3000));

    PayGroups payGroups = new PayGroups(pays);

    Long naverPaySum = payGroups.getNaverPaySum();

    assertEquals(naverPaySum, 3000);
}
```

# Reference
- https://jojoldu.tistory.com/412