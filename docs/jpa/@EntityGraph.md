---
layout: default
title: @EntityGraph
parent: JPA
---


Table of contents


- TOC
{:toc}


---

# @EntityGraph

연관관계가 있는 엔티티를 조회할 경우 지연로딩으로 설정되어 있다면 연관관계에 종속된 엔티티는 쿼리 실행 시 select 되지 않고 프록시 객체를 만들어 엔티티에 적용한다. 그 후 프록시 객체를 호출하게 되면 그때 select 쿼리가 실행된다.

연관관계가 지연로딩으로 되어 있어도 상황에 따라 즉시로딩이 필요할 때가 있다. 이 때 연관관계의 설정을 변경하지 않고 fetch 조인을 사용하여 해결할 수 있는데 `@EntityGraph`는 Data JPA에서 fetch 조인을 어노테이션으로 사용할 수 있도록 제공하는 기능이다.

```java
public interface AccumulationRepository extends JpaRepository<Accumulation, Long> {
    @EntityGraph(attributePaths = {"deposit"}, type = EntityGraph.EntityGraphType.FETCH)
    Page<Accumulation> findByPromotionId(Long promotionId, Pageable pageable);
}
```

- EntityGraphType.FETCH: attributePaths에 정의한 멤버변수만 EAGER로 불러오고 나머지 멤버변수는 모두 LAZY 전략을 적용한다.

- EntityGraphType.LOAD: attributePaths에 정의한 멤버변수만 EAGER로 불러오고 나머지 멤버변수는 정의되어 있는 FetchType을 따라간다.