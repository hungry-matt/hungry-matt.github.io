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

## 

# Reference
- https://jojoldu.tistory.com/412