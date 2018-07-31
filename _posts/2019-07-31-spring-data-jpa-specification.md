---
layout: post
title: Sping Data JPA Specification 의 여러가지 활용법
category: Development
comments: true
---

Spring Data JPA 를 쓰면서, 다양한 어려움에 부딪히게 된다. 그 중 정적인 쿼리는 @Query (+@Modifying) 등의 도움으로 JPQL로 작성하면 많은 부분을 해결할 수 있지만, 동적으로 쿼리를 생성해야할 경우엔 `interface Specification` 를 이용하여 쿼리를 빌드하는 단계가 필요해진다.

프로젝트의 사이즈와 복잡성이 커짐에 따라 쿼리 빌드하는 것도 점점 난도가 올라가는 경향이 있다.

아직 사내에서는 JPA를 hard하게 사용하는 프로젝트가 내 프로젝트밖에 없는 것 같기도 하고,
직접 사용해보며 겪은 몇몇 상황에서 적용한 코드를 정리하는 시간을 가져보면 좋을 것 같다는 생각이 들었다.


### 사용하려면,
1. Repository interface 에서 아래와 같이 두 인터페이스를 확장한다.

```java
public interface ClientRepository extends JpaRepository<Client, Long>, JpaSpecificationExecutor<Client> {

}
```

2. Repository를 사용하는 Service Layer (어떤이에게는 Controller Layer) 에서 Specification 을 사용할 수 있는 `JpaSpecificationExecutor`의 5가지 메소드를 활용

```java
    @Transactional(readOnly = true)
    public Page<Client> getClientsInGroup(long groupNo, Pageable pageable, Map<String, Object> filter) {
        return clientRepository.findAll(
                where(ClientSpecification.searchClients(filter))
                .and(ClientSpecification.clientsOfGroup(groupNo))
                , pageable
        );
    }

    @Transactional(readOnly = true)
    public Page<Project> retrievePageableProjects(Pageable pageable, Map<String, Object> filter) {
        Specifications<Project> spec = where(ProjectSpecification.searchProjects(filter))
                .and(ProjectSpecification.managersInProject(filter));

        return projectRepository.findAll(spec, pageable);
    }
```

### 여러가지 예제들
#### 가장 간단한 equal

```java
public static Specification<Client> clientsOfCorporate(long corporateNo) {
        return (root, query, cb) ->
                cb.equal(root.get(Client_.corporate).get(Corporate_.no), corporateNo);
    }
```


#### Join
```java
    public static Specification<Client> clientsOfGroup(long groupNo) {
        return (root, query, cb) -> {
            Join<Client, ClientGroup> join = root.join(Client_.groups);
            return cb.equal(join.get(ClientGroup_.group).get(Group_.no), groupNo);
        };
    }
```

#### 검색 파라미터를 하나의 Map 타입으로 받았을 때,
(switch 문이 보기 좋진 않지만...)
```java
public static Specification<Notice> searchNotices(Map<String, Object> filter) {
        return (root, query, cb) -> {
            List<Predicate> predicates = new ArrayList<>();

            filter.forEach((key, value) -> {
                String likeValueString = "%" + value + "%";
                switch (key) {
                    case "no":
                        predicates.add(cb.like(root.get(key).as(String.class), likeValueString));
                        break;

                    case "title":
                    case "tags":
                        predicates.add(cb.like(cb.lower(root.get(key)), likeValueString));
                        break;

                    case "writer":
                        Path<Manager> managerPath = root.get(key);
                        Path<String> filteringName = managerPath.get(Manager_.name);
                        predicates.add(cb.like(filteringName, likeValueString));
                        break;

                    case "createdAt":
                        Path<LocalDateTime> createdAt = root.get(Notice_.createdAt);
                        predicates.add(cb.like(createdAt.as(String.class), likeValueString));
                        break;
                }
            });

            predicates.add(cb.equal(root.get("active"), true));
            return cb.and(predicates.toArray(new Predicate[0]));
        };
    }
```

#### case 를 사용하고 싶다
(제네릭에 유의)
```java
cb.like(
        cb.<String>selectCase()
                .when(cb.equal(root.get(Project_.accountType), AccountType.CORPORATE), root.join(Project_.corporate, JoinType.LEFT).get(Corporate_.name))
                .when(cb.equal(root.get(Project_.accountType), AccountType.INDIVIDUAL), root.get(Project_.accountName))
                .otherwise(cb.nullLiteral(String.class)),
        likeValueString
)
```

#### 연관 관계 매핑이 안 되어있는 테이블과 조인하여 검색하고 싶다
**그러나, 그것은 불가능.** (ORM에서 연관관계가 없는 테이블과 조인하는 것 자체가 원칙적으로는 불가능. => Hibernate 5.2.10 이상 버전의 경우, JPQL에서 명시적으로 조인할 경우엔 가능)
대신, 대상 테이블에 먼저 질의한 결과를 fetch 한 후, `In` clause 사용으로서 ORM의 단점 극복
```java
    @Transactional(readOnly = true)
    public Page<Quotation> retrieveQuotations(Pageable pageable, Map<String, Object> filter) {
        Specification<Quotation> specification = searchQuotations(filter);

        specification = where(addIfAccountNamePresentSpecification(filter, specification))
            .and(excludeRemoved());

        return quotationRepository.findAll(specification, pageable);
    }

    private Specification<Quotation> addIfAccountNamePresentSpecification(Map<String, Object> filter, Specification<Quotation> specification) {
        Object accountNameObj = filter.get(Quotation_.accountName.getName());
        boolean shouldAddSpec = !StringUtils.isEmpty(accountNameObj);
        if (shouldAddSpec) {
            String accountName = (String) accountNameObj;
            Set<Long> corporateNumbers = corporateService.retrieveCorporateNumbersByNameContaining(accountName);
            specification = where(specification).and(
                    where(accountNoInCorporates(corporateNumbers))
                      .or(searchAccountName(accountName))
            );
        }
        return specification;
    }
```

#### 연관을 많이 맺은 Entity에서 N + 1 Problem 이 말썽
`JpaSpecificationExecutor` 의 메소드를 오버라이드 + `@EntityGraph` 사용으로 fetch 전략을 세움
(+ 단순한 `findOne` 정도는 N+1 이 발생해도 괜찮다는 개인적인 의견이 있음. Presentation Layer 에서 보여줄 필요가 없는 attribute가 있을 경우, Lazy Loading 의 이점이 더 크다고 생각하기 때문)
```java
    @Override
    @EntityGraph(attributePaths = {
            "corporate", "corporate.category", "corporate.subCategory",
            "client",
            "quotations", "quotations.deferredPaymentAgreement", "quotations.items",
            "mainManager", "subManager1", "subManager2",
            "projectCheckList"
    })
    Page<Project> findAll(Specification<Project> spec, Pageable pageable);
```


### 결론으로는 내 프로젝트엔 @Query 사용이 하나도 없다. (아직은)
(아직은 batch update, delete 가 없어서...)
또 다른 어려운 상황에 부딪힌다면, 아직 사용해보지 않은 `Specification` 의 API를 더 찾아보게 될 것 같다.
연관관계가 없는 테이블의 조인을 시도해보다, queryDsl-jpa 도 POC 해봤으나 `querydsl-jpa === spring-data 의 Specification => true` 인 결론이 났다.
querydsl의 프로젝트에는 `querydsl-sql` 이라는 서브프로젝트도 있는데, 이건 sql framework라고 하니, jooq와 비슷하게 사용이 가능할 것으로 보인다.
하지만, ORM을 쓰기로 했다면 ORM 으로 해결하는 것이 최선일 듯.
오해를 불러 일으킬 수도 있는 문장이지만
사실, 뭔가 잘 안 되면 설계(기획+요구사항+DB설계+legacy 등)를 잘못한 것이다. `연관관계가 없는 테이블의 조인`이 필요한 상황이 생긴 원인은 설계가 잘못됐기 때문이라고 생각한다.

#### 보너스로 Hibernate 종속적이지만 유용한 Annotation 몇 개 소개
Entity에 붙이는 Annotation 이다.
```java
    @OneToMany(fetch = FetchType.LAZY, mappedBy = "project")
    @OrderBy(value = "isFinal desc, no desc") // JPQL 문법을 따름
    @Where(clause = "payment_status != 'REMOVED'") // JPQL 문법을 따름
    private Set<Quotation> quotations;

    @Formula("(select count(c.no) from client c where c.corporate_no = no)") // SQL fragment 를 attribute로 지정 가능
    private Long clientsCount;
```