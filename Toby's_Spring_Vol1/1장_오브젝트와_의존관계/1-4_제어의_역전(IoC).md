# 1장 오브젝트와 의존관계

## 1-4 제어의 역전(IoC)
---
### 1.4.1 오브젝트 팩토리
#### 사실 UserDaoTest 는 UserDao 의 기능이 잘 동작하는지를 테스트하려고 만든 것이다.
#### 그런데 지금은 UserDao 와 ConnectionMaker 구현 클래스의 오브젝트를 만드는 것과, 그렇게 만들어진 두개의 오브젝트가 연결돼서 사용되도록 관계를 맺어주고 있다.
#### 이렇게 테스트라는 기능이과 다른 기능을 내포하고있으니 한번 분리해 보자!

---

### 팩토리.
#### 객체의 생성 방법을 결정하고 그렇게 만들어진 오브젝트를 돌려주는 클래스를 만들어 보자.
#### 이런일을 하는 오브젝트를 흔히 팩토리 라고 부른다.
#### 이는 디자인 패턴에서 말하는 특별한 문제를 해결하기 위해 사용되는 추상 팩토리 패턴이나 팩토리 메소드 패턴과는 다르니 혼동하지 말자!
### 단지 분리하려는 목적으로 사용하는 것이다, 어떻게 만들지(팩토리 메서드 패턴) 와 어떻게 사용할지(팩토리) 는 분명 다른 관심이다.
```java
public class DaoFactory {
    public UserDao userDao() {
        // 팩토리의 메소드는 UserDao 타입의 오브젝트를 어떻게 만들고, 어떻게 준비시킬지를 결정한다.
        ConnectionMaker connectionMaker = new DConnectionMaker();
        UserDao userDao = new UserDao(connectionMaker);
        return userDao;
    }
}
```
#### UserDaoTest 는 이제 UserDao 가 어떻게 만들어지는지 어떻게 초기화되어 있는지에 신경쓰지 않고 팩토리로부터 UserDao 오브젝트를 받아다가,
#### 자신의 관심사인 테스트를 위해 활용하기만 하면 끝난다.
#### UserDaoTest 는 다음과 같다
```java
public class UserDaoTest {
    public static void main(String[] arge) throws ClassNotFoundException, SQLException {
        UserDao dao = new DaoFactory().userDao();
    }
}
```
### 이러한 DaoFactory 를 분리했을 때 얻을 수 있는 장점은 매우 다양하다.
### 그중에서도 애플리케이션의 컴포넌트 역할을 하는 오브젝트와 (UserDao, ConnectionMaker, DConnectionMaker) , 
### 애플리케이션의 구조를 결정하는 오브젝트를 불리했다는 데 가장 의미가 있다. (DaoFactory)

---

### 1.4.2 오브젝트 팩토리의 활용
#### 하지만 UserDao가 아닌 다른 DAO의 생성 기능을 넣으면 어떻게될까?
#### 예로 AccountDao , MessageDao 를 넣으면 어떤 ConnectionMaker 구현 클래스를 사용할지 결정하는 기능이 중복돼서 나타난다.
```java
public class DaoFactory {
    public UserDao userDao() {
        return new UserDao(new DConnectionMaker());
    }
    public AccountDao accountDao() {
        return new UserDao(new DConnectionMaker());
    }
    public MessageDao messageDao() {
        return new UserDao(new DConnectionMaker());
    }
}
```
#### 위와 같은 문제점을 우리는 초난간 DAO 의 코드에서 getConnection 메서드를 따로 만들어서 분리해낸 리팩토링 방법을 생각해 보자
```java
public class DaoFactory {
    public UserDao userDao() {
        return new UserDao(connectionMaker());
    }
    public AccountDao accountDao() {
        return new UserDao(connectionMaker());
    }
    public MessageDao messageDao() {
        return new UserDao(connectionMaker());
    }
    public ConnectionMaker connectionMaker() {
        return new DConnectionMaker();
    }
}
```

---
### 1.4.3 제어권의 이전을 통한 제어관계 역전
#### 제어의 역전이란 프로그램의 제어 흐름 구조가 뒤바뀌는 것이다.
#### 일반적으로 프로그램의 흐름은 main() 매소드 같이 프로그램이 시작되는 지점에서 다음에 사용할 오브젝트를 결정하고 생성하고 오브젝트내에 메소드를 호출하는 반복적인 작업이 이루어진다.
#### 또 일반적으로 클래스(오브젝트)는 자신이 사용할 기능과 역할을 자신의 내에서 정의하고 알고있어, 오브젝트는 능동적으로 자신이 사용할 클래스를 결정하고, 언제 어떻게 오브젝트를 만들지를 관장한다.
---
### 제어의 역전이란 이런 제어 흐름의 개념을 거꾸로 뒤집는 것이다.
#### 오프젝트가 자신이 사용할 오브젝트를 스스로 선택하지 않는다.
#### 생성하지도 않는다.
#### 모든 제어 권한을 자신이 아닌 다른 대상에 위임한다.
#### main()과 같은 엔트리 포인트를 제외하면 모든 오브젝트는 이렇게 위임받은 제어 권한을 갖는 특별한 오브젝트에 의해 결정되고 만들어진다.
---
### 제어의 역전 개념은 사실 이미 폭넓게 적용되어 있다.
#### 서블릿을 생각해 보자. 서블릿을 개발해서 서버에 배포할 수는 있지만,
#### 실행을 개발자가 직접 제어할 수 있는 방법은 없다. 서블릿 안에 main() 메소드가 있어서 직접 실행시킬 수 있는 것도 아니다.
#### 대신 서블릿에 대한 제어 권한을 가진 컨테이너가 적절한 시점에 서블릿 클래스의 오브젝트를 만들고 그 안에 메소드를 호출한다.
#### 이렇게 서블릿이나 , JSP , EJB 처럼 컨테이너 안에서 동작하는 구조는 간단한 방식이긴 하지만 제어의 역전 개념이 적용되어 있다고 볼 수 있다.
---
### 템플릿 메소드를 생각해보자.
#### 템플릿 메소드에서 하위객체는 추상 메소드를 구현하지만 사용을 자신이 결정하지 않는다.
#### 즉 제어권을 상위 템플릿 메소드에 넘기고 자신은 필요할 때 호출되어 사용되도록 한다는, 제어의 역전 개념을 발견할 수 있다.
#### 템플릿 메소드는 제어의 역전이라는 개념을 활용해 문제를 해결하는 디자인 패턴이라고 볼 수 있다. 
#### 그래서 스프링에서 템플릿 메소드 패턴이 중요하기도 하다 그 예로 DispatcherServlet 에서 사용하고 있다.
### 참고 블로그 : 
[[Spring & Design Pattern] Spring에서 발견한 디자인패턴_template method pattern](https://sabarada.tistory.com/19)
---
### 프레임워크에대해 살펴보자.
#### 프레임워크와 라이브러리의 차이는 라이브러리를 사용하는 코드는 흐름을 사용자가 직접 제어 할 수 있다.
#### 그러나 프레임워크를 사용하는 코드는 사용자가 흐름을 주도하는 것이 아닌 프레임워크가 제어하게 된다.
#### 즉 프레임워크는 분명한 제어의 역전 개념이 적용되어 있어야 한다.
---
### 우리가 만들어 놓은 코드를 보자.
#### 우리가 만든 UserDao와 DaoFactory 에도 제어의 역전이 적용되어 있다.
#### 원래 ConnectionMaker 의 구현 클래스를 결정하고 오브젝트를 만드는 제어권은 UserDao에 있었다.
#### 그런데 지금은 DaoFactory 에게 있다. 자신이 어떤 ConnectionMaker 구현 클래스를 만들고 사용할지를 결정할 권한을 DaoFactory 에 넘겼으니
#### UserDao 는 이제 능동적이 아니라 수동적인 존재가 됐다. UserDao 도 Factory 에 의해 수동적으로 만들어지고 자신이 사용할 오브젝트도 DaoFactory 가 
#### 공급해주는 것을 수동적으로 사용해야하는 입장이 되었다.
---
### 즉 자연스럽게 관심을 분리하고 책임을 나누고 유연하게 확장 가능한 구조로 만들기 위해 DaoFactory 를 도입했던 과정이 바로 IoC 를 적용하는 작업이었다고 볼 수 있다.
## 스프링은 IoC 를 모든 기능의 기초가 되는 자바기술로 삼고 있으며, IoC를 극한까지 적용하고 있는 프레임워크다. 