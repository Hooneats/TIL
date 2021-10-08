# 1장 오브젝트와 의존관계

## 1-3 DAO의 확장
---
#### **객체지향은 세상의 모든것을 표현할 수 있게 해준다. 그리고 세상의 대부분은 더 전문적이고, 세분화되며, 더 넓어진다.**
#### **이런면에서 프로그램에 있어 확장이란 것은 매우 중요한 것이라 생각되어진다.**
---
### [1-3-1 클래스의 분리]()
#### 앞서 진행한 상속이라는 방법은 객체간의 의존성을 내포하고 있어 조금 불편하게 느껴진다.
#### 이번에는 아예 상속관계도 아니고 완전히 독립적인 클래스로 만들어 보겠다.
#### 방법은 별도의 클래스를 만드는 것이다. (SimpleConnectionMaker.java)
```java
public class SimpleConnectionMaker {

    public Connection makeNewConnection() throws ClassNotFoundException, SQLException {
        Class.forName("com.mysql.jdbc.Driver");
        Connection c = DriverManager.getConnection(
                "jdbc:mysql://localhost/springbook", "spring", "book"
        );
        return c;
    }
}
```
#### UserDao.java
```java
public class UserDao {
    private SimpleConnectionMaker simpleConnectionMaker;

    public UserDao() {
        // 상태를 관리하는 것도 아니니 한 번만 만들어 인스턴스 변수에 저장해두고 메소드에서 사용하게 된다.
        simpleConnectionMaker = new SimpleConnectionMaker();
    }
    public void add(User user) throws ClassNotFoundException, SQLException {
        Connection c = simpleConnectionMaker.makeNewConnection();
       
        ...
    }
}
```
### 그러나 아직도 이 코드상에는 의존성이 분리 되지 않았다.
####   DB 를 가져오는 방식이 (SimpleConnection.java) 변경이된다면 UserDao.java 코드 또한 변경해줘야 하기때문이다.

---

### [1-3-2 인터페이스의 도입]()
#### 가장 좋은 해결책은 두 개의 클래스가 서로 긴밀하게 연결되어 있지 않도록 중간에 추상적인 느슨한 연결고리를 만들어주는 것이다.
####   추상화란 어떤 것들의 공통적인 성격을 뽑아내어 이를 따로 분리해내는 작업이다.
####     이러한 추상화에 가장 유용한 도구가 바로 인터페이스이다!

#### 인터페이스는 어떤 일을 하겠다는 기능만 정의해놓은 것이다. 구현방법은 나타나 있지 않다.(ConnectionMaker.java)
```java
public interface ConnectionMaker {
    public Connection makeConnection() throws ClassNotFoundException, SQLException;
}
```
#### ConnectionMaker 의 구현 클래스 DConnectionMaker.java
```java
public class DConnectionMaker implements ConnectionMaker{
    public Connection getConnection() throws ClassNotFoundException, SQLException {
        // D 사 DB Connection 사용
        return null;
    }

    @Override
    public Connection makeConnection() throws ClassNotFoundException, SQLException {
        // D 사의 독자적인 방법으로 Connection 을 생성하는 코드가 오면 된다.
        return null;
    }
}
```
#### ConnectionMaker 인터페이스를 사용해 DConnectionMaker 기능을 쓰는 UserDao.java
```java
public class UserDao {
//public abstract class UserDao {
    private ConnectionMaker connectionMaker;
    
    public UserDao() {
        connectionMaker = new DConnectionMaker();
    }

    public void add(User user) throws ClassNotFoundException, SQLException {
        Connection c = simpleConnectionMaker.makeNewConnection();
        ...
    }
```
### 그러나 아직도 완벽하게 분리되어지지 않았다!
### 왜냐하면 **connectionMaker = new DConnectionMaker();** 에서 new DConnectionMaker() 구현체를 명시하는 코드가 있기 때문이다.
###  구현체 병경시 UserDao.java 의 코드 또한 변경하게 되는 일이 생기게 된다.