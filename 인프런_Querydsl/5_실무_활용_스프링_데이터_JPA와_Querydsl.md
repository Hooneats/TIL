## 실무 활용 - 스프링 데이터 JPA 와 QueryDsl

---

### 스프링 데이터 JPA 리포지토리로 변경
---

### 사용자 정의 리포지토리
#### 중요한 주의사항은 실제 사용할 Repository 인 MemberRepository 와 이름을 같게하여 그 뒤에 Impl 을 붙인다.
#### MemberRepositoryImpl.java 로 Querydsl 부분인 MemberRepositoryCustom 인터페이스를 받아 구현한뒤 MemberRepository 에 상속한다.

#### 만약 정말 하나에 특화된 복잡한 쿼리를 사용해야하는거면 그냥 MemberQueryRepository 클래스를 만들어 그냥 따로 사용해도된다.
#### MemberQueryRepository.java

---

### 스프링 데이터 페이징 활용1 - Querydsl 페이징 연동

---

### 스프링 데이터 페이징 활용2 - CountQuery 최적화
#### 카운트 쿼리가 생략 가능한 경우 생략해서 처리
- 페이지 시작이면서 컨텐츠 사이즈가 페이지 사이즈보다 작을때
- 마지막 페이지일 때 (offset + 컨텐츠 사이즈를 더해서 전체 사이즈 구함)

---

### 스프링 데이터 페이징 활용3 - 컨트롤러 개발

### 스프링 데이터 정렬(Sort)
#### 스프링 데이터 JPA는 자신의 정렬(Sort)을 Querydsl의 정렬(OrderSpecifier)로 편리하게 변경하는 기능을 제공한다.
#### 스프링 데이터 Sort를 Querydsl의 OrderSpecifier로 변환
```java
JPAQuery<Member> query = queryFactory
 .selectFrom(member);
for (Sort.Order o : pageable.getSort()) {
 PathBuilder pathBuilder = new PathBuilder(member.getType(),
member.getMetadata());
 query.orderBy(new OrderSpecifier(o.isAscending() ? Order.ASC : Order.DESC,
 pathBuilder.get(o.getProperty())));
}
List<Member> result = query.fetch();
```
####  참고: 정렬( Sort )은 조건이 조금만 복잡해져도 Pageable 의 Sort 기능을 사용하기 어렵다. 루트 엔티티
#### 범위를 넘어가는 동적 정렬 기능이 필요하면 스프링 데이터 페이징이 제공하는 Sort 를 사용하기 보다는
#### 파라미터를 받아서 직접 처리하는 것을 권장한다.
---

