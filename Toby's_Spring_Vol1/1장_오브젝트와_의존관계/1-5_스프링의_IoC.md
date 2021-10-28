# 1장 오브젝트와 의존관계

## 1-5 스프링의 IoC
---
### 스프링의 핵심을 담당하는 건, 바로 빈 팩토리 또는 애플리케이션 컨텍스트라고 불리는 것이다.

### 1.5.1 오브젝트 팩토리를 이용한 스프링 IoC
#### 스프링에서는 스프링이 제어권을 가지고 직접 만들고 관계를 부여하는 오브젝트를 빈 (Bean) 이라고 부른다.
#### 그리고 이러한 빈의 생성과 관계설정 같은 제어를 담당하는 IoC 오브젝트를 빈 팩토리 또는 애플리케이션 컨텍스트 라고 부른다.
#### 빈 팩토리라고 말할 때는 빈을 생성하고 관계를 설정하는  IoC의 기본 기능에 초점을 맞춘 것이고,
#### 애플리케이션 컨텍스트라고 말할 때는 애플리케이션 전반에 걸쳐 모든 구성요소의 제어 작업을 담당하는 IoC 엔진이라는 의미가 좀 더 부각된다고 보면 된다.

---
#### 애플리케이션 컨텍스트는 별도의 정보를 참고해서 빈(오브젝트)의 생성, 관계설정 등의 제어 작업을 총괄한다.
#### 기존 DaoFactory 코드에는 설정정보, 예를 들어 어떤 클래스의 오브젝트를 생성하고 어디에서 사용하도록 연결해줄 것인가 등에 관한 정보가 평범한 자바 코드로 만들어졌다.
#### 애플리케이션 컨텍스트는 이런 정보를 담고 있진 않지만, 별도의 설정정보를 담고 있는 무언가를 가져와 이를 활용하는 범용적인 IoC 엔진이라 볼 수 있다.
#### 앞에서는 DaoFactory 자체가 설정정보까지 담고 있는 IoC 엔진이었는데, 여기서는 자바 코드로 만든 애플리케이션 컨텍스트의 설정정보로 활용될 것이다.

---
### DaoFactory 를 사용하는 애플리케이션 컨텍스트
#### ApplicationContext 는 빈 팩토리를 위한 오브젝트 설정을 담당하는 클래스이다.
#### @Configuration 이라는 애노테이션을 붙여준다. 그리고
#### 오브젝트를 만들어주는 메소드에는 @Bean 이라는 애노테이션을 붙여준다.
#### UserDao() 메소드는 UserDao 타입  오브젝트를 생성하고 초기화해서 돌려주는 것이니 당연히 @Bean 이 붙는다.
#### 또한 ConnectionMaker 타입의 오브젝트를 생성해주는 connectionMaker() 메소드에소 @Bean 을 붙여준다.
### 이 두가지 애노테이션만으로 IoC 방식의 기능을 제공할 때 사용할 완벽한 설정정보가 된 것이다.
```java

@Configuration
public class DaoFactory {
    @Bean
    public UserDao userDao() {
        return new UserDao(connectionMaker());
    } 
    @Bean
    public ConnectionMaker connectionMaker() {
        return new DConnectionMaker();
    }
} 
```
#### 아래는 이렇게 등록한 @Bean 과 설정 정보를 사용하는 ApplicationContext 타입의 오브젝트이다.
```java
public class UserDaoTest {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        ApplicationContext context = new AnnotationConfigApplicationContext(DaoFactory.class);
        UserDao dao = context.getBean("userDao", UserDao.class);
    } 
} 
```
#### @Configuration 이 붙은 자바 코드를 설정정보로 사용하려면 AnnotationConfigApplicationContext 를 이용하면 된다.
#### ApplicationContext 의 getBean() 이라는 메소드를 이용해 UserDao 의 오브젝트를 가져올 수 있다.
#### getBean() 메소드는 ApplicationContext 가 관리하는 오브젝트를 요청하는 메소드다.
#### getBean() 의 파라미터인 "userDao" 는 ApplicationContext 에 등록된 빈의 이름이다.
#### @Bean 이라는 애노테이션을 userDao 라는 이름의 메소드에 붙였는데, 이메소드 이름이 바로 빈의 이름이다(빈 이름생성전략-메소드이름으로)
#### getBean() 은 기본적으로 Object 타입으로 리턴하게 되어 있어서 매번 리턴되는 오브젝트에 다시 캐스팅을 해줘야 하는 부담이 있다.
#### 자바 5 이상의 제네릭 메소드 방식을 사용해 getBea() 의 두 번째 파라미터에 리턴 타입을 주면, 지저분한 캐스팅 코드를 사용하지 않아도 된다. (ex: context.getBean("userDao", UserDao.class);)
#### 스프링의 기능을 사용했으니 필요한 라이브러리를 추가해줘야 한다.

---
### 애플리케이션 컨텍스트의 동작방식
#### 오브젝트 팩토리에 대응되는 것이 스프링의 애플리케이션 컨텍스트다.
#### 스프링에서는 이 애플리케이션 컨텍스트를 IoC 컨테이너라 하기도 하고, 빈 팩토리라고 부를 수도 있다.
#### ApplicationContext 인터페이스를 구현하는데, ApplicationContext 는 빈 팩토리가 구현하는 BeanFactory 인터페이스를 상속했으므로 애플리케이션 컨텍스트는 일종의 빈 팩토리인 샘이다.
#### 애플리케이션 컨텍스트가 스프링의 가장 대표적인 오브젝트이므로, 애플리케이션 컨텍스트를 그냥 스프링이라고 부르는 개발자도 있다.
#### 애플리케이션 컨텍스트는 애플리케이션에서 IoC 를 적용해서 관리할 모든 오브젝트에 대한 생성과 관계설정을 담당한다.
#### 대신 ApplicationContext 에는 DaoFactory 와 달리 직접 오브젝트를 생성하고 관계를 맺어주는 코드가 없고, 
#### 그런 생성정보와 연관관계 정보를 별도의 설정정보를 통해 얻는다. 때로는 외부의 프로젝트에 그 작업을 위임하고 그 결과를 가져다가 사용하기도 한다.
#### 애플리케이션 컨텍스트는 @Configuration 이 붙은 클래스를 설정정보로 등록해두고 @Bean 이 붙은 메소드의 이름을 가져와 빈 목록을 만들어둔다.
#### 클라이언트는 getBean() 메소드를 호출하면 빈을 생성하는 메소드를 호출해서 오브젝트를 생성시킨 후 클라이언트에 돌려준다.
#### 애플리케이션 컨텍스트를 사용하는 이유는 범용적이고 유연한 방법으로 IoC 기능을 확장하기 위해서이다. 또한 다음의 장점들이 있다.
1. 클라이언트는 구체적인 팩토리 클래스를 알 필요가 없다.
2. 애플리케이션 컨텍스트는 종합 IoC 서비스를 제공해준다.
3. 애플리케이션 컨텍스트는 빈을 검색하는 다양한 방법을 제공한다.

---
### 스프링 IoC 의 용어 정리
### 빈 : 
####스프링이 IoC 방식으로 관리하는 오브젝트
### 빈 팩토리 : 
#### IoC 를 담당하는 핵심 컨테이너를 가리킨다. 빈을 등록, 생성, 조회하고 돌려준다. 보통은 빈 팩토리를 바로 사용하지않고 이를 확장한 애플리케이션 컨텍스트를 이용한다.
#### BeanFactory 라고 붙여쓰면 빈 팩토리가 구현하고 있는 가장 기본적인 인터페이스의 이름이 된다. 이 인터페이스에 getBean() 과 같은 메소드가 정의되어 있다.
### 애플리케이션 컨텍스트 : 
#### 빈 팩토리를 확장한 IoC 컨테이너이다. 기본적인 기능은 빈 팩토리와 동일하다. 여기에 스프링이 제공하는 각종 부가 서비스를 추가로 제공한다.
#### ApplicationContext 는 BeanFactory 를 상속한다.
### 설정정보 / 설정 메타정보
#### 