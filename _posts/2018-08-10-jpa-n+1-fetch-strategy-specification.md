---
layout: post
title: Sping JPA fetch 전략은 Specification 에서 세우자
category: Development
comments: true
---

Spring Data JPA 를 쓰면서, 다양한 어려움에 부딪히게 된다. 
그 중 연관관계 매핑을 하며, 조회시 발생하는 N + 1 문제는 ORM 의 기본 메커니즘 때문에 필연적으로 마주치게 되는 문제이다.

회사에서 진행중인 프로젝트에서 알려진 여러 방법으로 N+1 문제를 회피하기 위한 여러 방법을 써봤지만, 조금 전 나만의 결론을 짓게 되었다.

### `fetch 전략은 Specification.toPredicate() 에서`

나는 @Query 의 사용을 지양한다. 그래서 조회 쿼리를 위해서 JPA Specification 을 주로 사용한다.

그러는 이유는 아무리 native query가 아니고 JPQL 이라 하더라도, SELECT 로 시작하는 문자열 코드를 최대한 남기고 싶지 않은 이상한 성향 때문인데, 이것 때문에 오히려 좀 더 많은 코드를 작성해야할 경우도 있지만 나는 이게 더 ORM 을 쓰는 이유와 부합하다고 굳건히 믿으며 그렇게 @Query 를 사용하지 않고 Specification 을 사용하고 있다.

그러다 프로젝트의 사이즈가 커지면서 하나의 엔티티가 8개의 엔티티와 연관관계를 맺고 있었고, 그 8개의 엔티티는 단방향/양방향으로 또 다른 엔티티들과의 수많은 연관관계가 생기게 되었다.

그나마 회사 내부에서 사용하는 백오피스 앱이라, 단일 조회일 경우엔 그나마 크게 부담 없이 N+1 의 쿼리가 날아가도 괜찮았지만 30개, 50개, 100개씩 목록을 조회하는 쿼리의 경우엔 문제가 심각했다.

하나의 엔티티의 목록을 조회하기 위해 6 - 8 초가 걸리는 것은 분명 문제가 있는 것이었다. (연관관계가 4-6개까지일 땐 그래도 이렇게 오래걸리진 않았다.)

이 문제를 해결하기 위해 평소 멀리서만 좋아하는 [이동욱님의 블로그](http://jojoldu.tistory.com/165) 를 참고하여 문서를 찾아보며 `@EntityGraph` 로 해결을 일단락 하는듯 하였으나, 여전히 원치않는 N+1 쿼리가 발생하였다. (* 정확하진 않으나, Specification 과 함께 사용하면 EntityGraph 가 무시되는 것 같다.)

그 다음으로 찾아본 곳이 [럭키Dragon님의 블로그](http://blog.naver.com/PostView.nhn?blogId=anbv3&logNo=220883456337&parentCategoryNo=&categoryNo=31&viewDate=&isShowPopularPosts=true&from=search) 인데 여기서 Specification 의 힌트를 얻었다. 

근데 바로 적용해보려니 잘 안 되었다. (`query specified join fetching, but the owner of of the fetched ...`) 의 오류 메시지와 함께.


그 다음으로 [관련 질문 SO](https://stackoverflow.com/questions/27909100/eager-fetching-in-a-spring-specification)와 [스프링 지라 이슈](https://jira.spring.io/browse/DATAJPA-105)에 도달하게 되었다.

그래서 지금은 아래와 같이 해결하였다. (Before 와 After 를 비교)


#### Code (Before)

```java
// ProjectRepository.java
    @Override
    @EntityGraph(attributePaths = {
            "corporate", "corporate.category", "corporate.subCategory",
            "client", "client.corporate",
            "quotations", "quotations.deferredPaymentAgreement", "quotations.items",
            "mainManager", "subManager1", "subManager2",
            "taxInvoiceDemands",
            "projectCheckList"
    })
    Page<Project> findAll(Specification<Project> spec, Pageable pageable);

// ProjectSpecification.java
    public static Specification<Project> searchProjects(Map<String, Object> filter) {
        return (root, query, cb) -> {
            List<Predicate> predicates = new ArrayList<>();
            
            // ... where clause 구현 코드

            return cb.and(predicates.toArray(new Predicate[0]));
        };
    }
```


#### Code (After)

- 그렇게 깔끔한 코드인 건 아니다.

``` java
// ProjectRepository.java
// override 한 findAll 메소드를 삭제 :: interface 의 메소드 그대로 사용

// ProjectSpecification.java
    public static Specification<Project> searchProjects(Map<String, Object> filter) {
        return (root, query, cb) -> {
            List<Predicate> predicates = new ArrayList<>();
            
            // ... where clause 구현 코드

            query.distinct(true); // 카테시안 곱 방지
            if (Project.class.equals(query.getResultType())) { // data query 와 count query 를 구분
                root.fetch(Project_.corporate, JoinType.LEFT);
                root.fetch(Project_.client, JoinType.LEFT);
                root.fetch(Project_.mainManager, JoinType.LEFT);
                root.fetch(Project_.subManager1, JoinType.LEFT);
                root.fetch(Project_.subManager2, JoinType.LEFT);
                root.fetch(Project_.taxInvoiceDemands, JoinType.LEFT);
                root.fetch(Project_.quotations, JoinType.LEFT);
            }

            return cb.and(predicates.toArray(new Predicate[0]));
        };
    }
```

처음에는 `query.getResultType()`을 체크하는 if 문 없이 했다가 `query specified join fetching, but the owner of of the fetched ...` 메시지를 동반한 오류가 발생했다.

Hibernate 는 data 조회 쿼리와 count 조회 쿼리, 이 2개의 쿼리를 날리는데 둘 다 이 Specification 의 조건을 타게 된다. count 쿼리를 호출할 땐, Long 타입 하나 뿐이고 다른 필드(연관관계)가 없으므로 `root.fetch(...)` 라고 명시적으로 선언한 join 을 수행하지 못해 에러가 발생하는 것이다.

그래서 count query일 땐 skip 하도록 처리한 workaround 코드이다.

결과적으로 목록 50개를 조회할 때 약 4~5초 정도 걸리던 쿼리가 이젠 1초 정도 걸리게 되었다.

날아가는 쿼리를 보니, 한번에 Join 하는 테이블이 7개... 그러나 메타성 테이블도 포함되어 있어 성능문제를 야기할 것 같진 않다. 그리고 백오피스라, DB 조회를 좀 많이 하더라도 괜찮을 것 같다.

이 과정에서 불필요한 `@OneToOne` 연관관계 하나를 `@Embeddable` + `@Embedded` 로 없애 버렸다.

이 과정들을 통해 EAGER/LAZY, FETCH/LOAD 의 동작 방식에 대해 좀 더 구체적으로 알게 되었고, 설계적인 관점에서 좀 더 생각을 하게 된 것 같다.
=> 모든 엔티티의 연관관계의 글로벌 로딩 전략은 LAZY로, 필요한 곳에서, 필요에 따라 Business Layer 에서 Fetch 전략을 세우는 것이 객체지향적인 관점에서도 부합하는 듯