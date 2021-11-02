## 스프링 데이터 JPA 가 제공하는 Querydsl 기능
### (아쉽게도 아직은 제약이 많아서 복잡한 실무에서는 사용하기 힘들다)
---

### 인터페이스 지원 - QuerydslPredicateExecutor

#### 한계점 :
1. 조인을 쓸수 없다.
2. 클라이언트가 Querydsl 에 의존해야 한다.
3. 복잡한 실무환경에서 사용하기에는 한계가 명확하다.
---

### Querydsl Web 지원
#### 한계점
1. 단순한 조건만 가능
2. 조건을 커스텀하는 기능이 복잡하고, 명시적이지 않다.
3. 컨트롤러가 Querydsl 에 의존
4. 복잡한 실무환경에서 사용하기에는 한계가 명확하다.

---

### 리포지토리 지원 - QuerydslRepositorySupport
#### 장점
1. getQuerydsl().applyPagination() 스프링 데이터가 제공하는 페이징을 Querydsl로 편리하게 변환 가능(단! Sort는 오류발생)
2. from() 으로 시작 가능(최근에는 QueryFactory를 사용해서 select() 로 시작하는 것이 더 명시적)
3. EntityManager 제공

#### 한계점
1. Querydsl 3.x 버전을 대상으로 만듬
2. Querydsl 4.x에 나온 JPAQueryFactory로 시작
3. 할 수 없음
4. select로 시작할 수 없음 (from으로 시작해야함)
5. QueryFactory 를 제공하지 않음
6. 스프링 데이터 Sort 기능이 정상 동작하지 않음

---

### Querydsl 지원 클래스 직접 만들기(위의 단점들 극복)
#### 장점
1. 스프링 데이터가 제공하는 페이징을 편리하게 변환
2. 페이징과 카운트 쿼리 분리 기능
3. 스프링 데이터 Sort 지원
4. select(), selectFrom() 으로 시작가능
5. EntityManager, QueryFactory 제공
#### QuerydslRepositorySupport
