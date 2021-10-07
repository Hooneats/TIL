# 1장 오브젝트와 의존관계

## 1-2 DAO의 분리
---
### [1. 관심사의 분리]
#### 앞서 작성한 코드의 책에서 말하는 문제점이 있다.
#### 책에서 말하는 문제점 :
##### add() 메서드 하나에만 적어도 3가지 관심사항이 있다.
##### -> DB 연결과 관련된 관심
##### -> Statement 를 만들고 실행하는 것
##### -> Statement 와 Connection 오브젝트를 담아서 소중한 공유 리소스를 시스템에 돌려주는 것
#### => 관심사가 같은 것끼리 모으고 다른 것은 분리해줌으로써 같은 관심에 효과적으로 집중 할 수 있도록 해야한다.
###### 참고로 예외사항에 대한 처리가 전혀 없는데 이는 나중에 다루도록 하자.
---
### [2. 중복코드의 메소드 추출]
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

---

### [3. DB 커넥션 만들기의 독립]
#### UserDao 의 커넥션을 만약 N 사와 D 사가 독자적으로 만든 방법으로 적용하고 싶어한다면 어떻게 해야할까?
#### 또는 UserDao 의 DB 커넥션을 가져오는 방법이 종종 변경된다면 어떻게 해야할까?
### -> 상속을 통한 확장을 제공할 수 있다.(그러나 상속은 슈퍼클래스와 서브클래스의 긴밀한 결합을 허용 및 상속구조에 따른 문제점이 있기에 좋은 방법이 아니다)
### 상속을 통해 제공할때 템플릿 패턴과 팩터리 패턴을 적용할 수 있다.

#### 우선 상속을 위해 UserDao 클래스를 abstract 클래스로 바꾸자
```java
public abstract class UserDao {
    ...
}
```
### 그 후 Connection 을 반환하는 getConnection() 메서드를 추상 메서드로 바꾸자
```java
public abstract Connection getConnection() throws ClassNotFoundException, SQLException;
```
### 이제 N 사와 D 사는 UserDao 를 상속받아 getConnection 을 구현하면 독자적인 getConnection 방법으로 UserDao 의 add() 와 get()을 사용할 수 있다.

### 여기서 사용된 디자인 패턴이 템플릿 메소드 패턴과, 팩토리 메소드 패턴이다.
#### 먼저 디자인 패턴을 사용하는 이유는 크게 두가지로 볼 수있다.
1. 재사용 가능한 솔루션이다.
2. 각 패턴의 핵심이 담긴 목적과 의도가 있기에 간단히 패턴 이름을 언급하는 것만으로도 설계의 의도와 해결책을 설명할 수 있다.

### 템플릿 메소드 패턴이란
- 상속을 통해 슈퍼클래스의 기능을 확장할 때 사용하는 가장 대표적인 방법이다.
- 변하지 않는 기능은 슈퍼클래스에 만들어두고 자주 변경되며 확장할 기능은 서브클래스에서 만들도록 하는 것이다.
- 이때 슈퍼클래스에서 디폴트 기능을 정의해두가나 비워뒀다가 서브클래스에서 선택적으로 오버라이드 할 수 있도록 만들어둔 메소드를 훅(hook) 메소드라 한다.

```java
public abstract class Super {
    public void templateMethod() {
        // 기본 알고리즘 코드
        hookMethod();
        abstractMethod();
    }
    // ->기본 알고리즘 골격을 담은 메소드를 템플릿 메소드라 부른다. 템플릿 메소드는 서브클래스에서 오버라이드하거나 구현할 메소드를 사용한다.

    protected void hookMethod() {
    } // -> 선택적으로 오버라이드 가능한 훅 메소드

    public abstract void abstractMethod(); // -> 서브클래스에서 반드시 구현해야 하는 추상 메소드
}

public class Sub extends Super {
    @java.lang.Override
    protected void hookMethod() {
        ...
    }

    @java.lang.Override
    public void abstractMethod() {
        ...
    }
}
```

### 팩토리 메소드 패턴이란
- 템플릿 메소드 패턴이랑 마찬가지로 상속을 통해 기능을 확장 하게하는 패턴이다. 그래서 구조도 비슷하다.
- 서브클래스에서 구체적인 오브젝트 생성 방법과 클래스를 결정할 수 있도록 미리 정의해둔 메소드를 팩토리 메소드라고 하고,
- 이 방식을 통해 오브젝트 생성 방법을 나머지 로직, 즉 슈퍼클래스의 기본 코드에서 독립시키는 방법을 팩토리 메소드 패턴이라 한다.
* 자바에서는 오브젝트를 생성하는 기능을 가진 메소드를 일반적으로 팩토리 메소드라고 부른다 이때, 팩토리 메소드 패턴의 팩토리 메소드와의 의미가 다르기에 주의하자.
