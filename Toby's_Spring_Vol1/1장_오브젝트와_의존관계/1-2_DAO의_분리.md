## 1장 오브젝트와 의존관계

### 1-2 DAO의 분리

#### 1. 관심사의 분리
#### 앞서 작성한 코드의 책에서 말하는 문제점이 있다.
#### 책에서 말하는 문제점 :
##### add() 메서드 하나에만 적어도 3가지 관심사항이 있다.
##### -> DB 연결과 관련된 관심
##### -> Statement 를 만들고 실행하는 것
##### -> Statement 와 Connection 오브젝트를 담아서 소중한 공유 리소스를 시스템에 돌려주는 것
#### => 관심사가 같은 것끼리 모으고 다른 것은 분리해줌으로써 같은 관심에 효과적으로 집중 할 수 있도록 해야한다.
###### 참고로 예외사항에 대한 처리가 전혀 없는데 이는 나중에 다루도록 하자.

#### 2. 중복코드의 메소드 추출
#### DB와의 연결 관심사인 Connection 코드를 분리해 보자
#### add() 와 get() 이 갖고있는 중복된 코드이기도 하다.
```java
    private Connection getConnection() throws ClassNotFoundException, SQLException {
        Class.forName("com.mysql.jdbc.Driver");
        Connection c = DriverManager.getConnection("jdbc:mysql://localhost/springbook", "spring", "book");
        return c;
    }
```
### 이처럼 중복된 코드를 관심의 종류에 따라 구분해 놓으면 한 가지 관심에 대한 변경이 일어날 경우 그 관심이 집중되는 부분의 코드만 수정하면 된다.
### 관심이 다른 코드가 있는 메소드에는 영향을 주지도 않을뿐더러, 관심 내용이 독립적으로 존재하므로 수정도 간단해졌다.
---
### => 로직을 작성할때 관심의 종류를  구분해 보자, 관심단위로 코드를 짜는 연습을 하고, 로직을 완성후 중복된코드 또는 분리할 관심이 있나 살펴보고
###    리팩토링을 해보자!