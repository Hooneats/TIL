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

---
### [1-3-3_관계설정_책임의_분리]()
#### 완전한 분리를 이룰려면 어떻게 해야할까?!
#### 클라이언트에게 구현객체의 선택을 맡기면된다.
#### 즉 우리는 클라이언트에게 선택을 맡기고 선택된것을 받아 넣어주면 되는것이다.
#### 이를 가능하게 할려면 클라이언트의 선택을 파라미터로 전달 가능하게하고, 파라미터로 제공받은 오브젝트는 인터페이스에 정의된 소스만 이용하면 된다.
#### 코드를 통해 보면 이해할 수 있을것이다.
```java
public UserDao(ConnectionMaker connectionMaker) {
    this.connectionMaker = connectionMaker;    
}
```
#### 클라이언트는 아래와 같이 선택해서 사용하면 된다.
```java
public static void main(String[] args) {
    ConnectionMaker connectionMaker = new DConnectionMaker();
    UserDao dao = new UserDao(connectionMaker);
}
```

---
### [1-3-4_원칙과 패턴]()
#### 개방폐쇄 원칙 (OCP , Open-Closed-Principle)
#### 깔끔한 설계를 위해 적용 가능한 객체지향 설계 원칙 중의 하나다.
#### '클래스나 모듈은 확장에는 열려 있어야 하고 변경에는 닫혀있어야 한다.'
#### UserDao 에 전혀 영향을 주지 않고도 얼마든지 기능을 확장할 수 있게 되게 만들었고,
#### UserDao 자신의 핵심 기능을 구현한 코드는 그런 변화에 영향을 받지 않고 유지할 수 있으므로 변경에는 닫혀 있다고 말할 수 있다.
---
### 객체지향 설계원칙
1. SRP (Single Responsibility Principle) : 단일 책임 원칙
2. OCP (Open Closed Principle) : 개방 폐쇄 원칙
3. LSP (Liskov Substitution Principle) : 리스코프 치환 원칙
4. ISP (Interface Segregation Principle) : 인터페이스 분리 원칙
5. DIO (Dependency Inversion Principle) : 의존관계 역전 원칙
---
### 높은 응집도와 낮은 응집도
#### 한마디로 말해 '높은 응집도와 낮은 결합도' 인 소프트웨어를 만들어야 한다.
#### 응집도가 높다는 것은 변화가 일어날 때 해당 모듈에서 변하는 부분이 크다는 것
#### 결합도는 하나의 오브젝트가 변경이 일어날 때에 관계를 맺고 있는 다른 오브젝트에게 변화를 요구하는 정도
#### 즉 결합도가 높아지면 변경에 따르는 작업량이 많아지고, 변경으로 인해 버그가 발생할 가능성이 높아진다.
---
### 전략 패턴
#### 전략 패턴은 디자인 패턴의 '꽃' 이다.
### 인터페이스를 통해 통째로 외부로 분리시키고, 이를 구현한 구체적인 알고리즘 클래스를 필요에 따라 바꿔서 사용할 수 있게 하는 디자인 패턴이다.
#### 여기서 말하는 알고리즘이란 독립적인 책임으로 분리가 가능한 기능을 뜻한다.
#### 또한 전략 패턴은 클라이언트의 필요성에 대해서도 잘 설명하고 있다.
#### 전략 패턴의 적용 방법을 보면 클라이언트의 역할이 잘 설명되어 있다.
#### 컨텍스트(UserDao)를 사용하는 클라이언트는 컨텍스트가 사용할 전략을 컨텍스트의 생성자 등을 통해 제공해주는게 일반적이다.
---
### 스프링이란 바로 지금까지 설명한 객체지향적 설계 원칙과 디자인 패턴에 나타난 장점을 자연스럽게 개발자들이 활용할 수 있게 해주는 프레임워크다.