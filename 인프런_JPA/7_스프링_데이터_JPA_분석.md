## 스프링 데이터 JPA 분석
---

### 스프링 데이터 JPA 구현체 분석
#### SimpleJpaRepository<T,ID>

---
### 새로운 엔티티를 구별하는 방법
#### save() 메소드 호출시
#### 새로운 엔티티면 persist()
#### 데이터에서 꺼내왔던 엔티티면 merge()를 호출한다.
#### 이 merge() 는 detached 된 데이터에만 사용해야하기에
#### 업데이트하거나 저장할경우 merge() 쓰지말고 꼭 persist() 쓰거나 persist() 가 쓰이도록 짜라!
#### save() 시에 persist() 가 동작할려면 Id 가 Null 이여야한다.
#### 때문에 따로 Id 를 넣어주었을경우 Persistable<> 을 사용해라!

---

