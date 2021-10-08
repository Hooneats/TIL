## 2.객체 생성과 파괴

### [3.private 생성자나 열거 타입으로 싱글턴임을 보증하라]()
#### 싱글턴은 spring 에서 @Service, @Controller, @Repository, @Component 등 Bean 을 생성하는 기본적인 전략으로 사용되는 패턴으로 사용된다.
#### 이러한 싱글톤에 대해 알아보자.

---

### 싱글턴을 만드는 방식은 보통 두가지 중에 하나이다.
### 1. 셍성자는 private 으로 감춰두고 public 정적 final 멤버로 인스턴스를 생성하는 방식
### 2. 생성자와 인스턴스를 생성하는 정적 final 멤버를 private 으로 두고 public 정적 팩터리 메서드를 통해 인스턴스를 생성하는 방식

---

### 1. 셍성자는 private 으로 감춰두고 public 정적 final 멤버로 인스턴스를 생성하는 방식 
```java
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() {}
    
    public void leaveTheBuilding() {...}
}
```
#### public 필드 방식의 가장 큰 장점은 해당 클래스가 싱글턴임이 API 에 명백히 드러난다는 것이다.
#### 두번째 장점으로는 바로 간결함이다.

#### private 생성자는 public static final 필드인 Elvis.INSTANCE 를 초기화 할 때 딱 한 번만 호출된다.
#### public 이나 protected 생성자가 없으므로 전체 시스템에서 인스턴스가 하나뿐임을 보장한다.
#### ---> 그러나 예외도 있다.
####   예외는 단 한 가지, 권한이 있는 클라이언트는 리플렉션 API 인 AccessibleObject.setAccessible 을 사용해 private 생성자를 호출할 수 있다.
####   이러한 공격을 벙어하려면 생성자를 수정하여 두 번째 객체가 생성되려 할 때 예외를 던지게 하면된다.
```java
Elvis elvis2 = Elvis.INSTANCE
Constructor<Elvis> constructor = (Constructor<Elvis>) elvis2.getClass().getDeclaredConstructor();
constructor.setAccessible(true);

// getDeclaredConstructors() 는 클래스의 private, public 등의 모든 생성자를 리턴해 준다.

Elvis elvis3 = constructor.newInstance();
assertNotSame(elvis2, elvis3); // FAIL
```
####   이를 해결하기 위해서는 두번째 생성자가 생성되려할 때 예외를 던지면 된다.
```java
private Elvis() {
    if(INSTANCE != null) {
        throw new RuntimeException("생성자를 호출 할 수 없습니다.");    
    }    
}
```

---

### 2. 생성자와 인스턴스를 생성하는 정적 final 멤버를 private 으로 두고 public 정적 팩터리 메서드를 통해 인스턴스를 생성하는 방식
```java
public class Elvis {
    private static final Elvis INSTANCE = new Elvis();
    private Elvis() {}
    
    public static Elvis getInstance() {
        return INSTANCE;
    }
    
    public vois leaveTheBuilding(){...}
}
```
#### Elvis.getInstance 는 항상 같은 객체의 참조를 반환하므로 제 2의 Elvis 인스턴스란 결코 만들어지지 않는다.
#### (그러나 역시 리플렉션을 통한 예외는 똑같이 적용된다.)

#### 정적 팩터리 방식의 첫번째 장점은 (마음이 바뀌면) API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다는 점이다.
####    유일한 인스턴스를 반환하던 팩터리 메서드가 (예컨대) 호출하는 스레드별로 다른 인스턴스를 넘겨주게 할 수 있다.
#### 정적 팩터리 방식의 두번째 장점은 원한다면 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있다는 점이다.
#### 정적 팩터리 방식의 세번째 장점은 정적 팩터리의 메서드 참조를 공급자(Supplier)로 사용할 수 있다는 점이다.
####    가령 Instance 를 Supplier<Elvis> 로 사용하는 식이다. 
```java
Supplier<Elvis> elvisSupplier = Elvis::getInstance;
Elvis elvis = elvisSupplier.get(); // 정적 팩터리의 메서드 참조를 공급자(Supplier)로 사용
```

---

### 두 방식의 문제점
#### 둘 중 하나의 방식으로 만든 싱글턴 클래스를 직렬화하려면 단순히 Serializable 을 구현한다고 선언하는 것만으로는 부족하다.
#### 모든 인스턴스 필드를 일시적 이라고 선언하고 readResolve 메서드를 제공해야 한다.
#### 이렇게 하지 않으면 직렬화된 인스턴스를 역직렬화할 때마다 새로운 인스턴스가 만들어진다.
#### (역직렬화는 기본 생성자를 호출하지 않고 값을 복사해서 새로운 인스턴스를 반환한다. 그때 통하는게 readResolve() 메서드이다.)
####   새로운 가짜 Elvis 탄생을 예방하고 싶다면 Elvis 클래스에 다음의 readResolve 메서드를 추가하자
```java
//싱글턴임을 보장해주는 readResolve 메서드
private Object readResolve() {
    // 진짜 Elvis 를 반환하고, 가짜 Elvis 는 가비지 컬렉터에 맡긴다.
    return INSTANVE;    
}
```

---

### 추가적으로 싱글턴을 생성하는 세 번째 방식이 있다.
### 3. 원소가 하나인 열거 타입을 선언하는 방법
```java
public enum Elvis {
    INSTANCE;
    
    public void leaveTheBuilding(){...}
}
```
#### public 필드 방식과 비슷하지만 더 간결하고, 추가 노력없이 직렬화할 수 있고,
####    아주 복잡한 직렬화 상황이나 리플렉션 공격에서도 제2의 인스턴스가 생기는 일을 완벽히 막아준다.
#### 대부분의 상황에서 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법이다.
#### 그러나 다른 클래스를 상속받을려면 이방식을 사용 할 수 없다.
####   (enum 은 기본적으로 Enum 클래스를 상속받고 있기 떄문이다.)

## 참고 블로그 : 
[[이펙티브 자바] 아이템 3. private 생성자나 열거 타입으로 싱글턴임을 보증하라](https://velog.io/@lychee/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%EC%9E%90%EB%B0%94-%EC%95%84%EC%9D%B4%ED%85%9C-3.-private-%EC%83%9D%EC%84%B1%EC%9E%90%EB%82%98-%EC%97%B4%EA%B1%B0-%ED%83%80%EC%9E%85%EC%9C%BC%EB%A1%9C-%EC%8B%B1%EA%B8%80%ED%84%B4%EC%9E%84%EC%9D%84-%EB%B3%B4%EC%A6%9D%ED%95%98%EB%9D%BC)
[이펙티브 자바 03. private 생성자, - enum 자료형, 싱글톤 패턴](https://plposer.tistory.com/64)
---
[헤드퍼스트 디자인 패턴: 5. 싱글턴 패턴](https://plposer.tistory.com/22?category=599723)
[[Design Pattern] 싱글턴 패턴(Singleton pattern)](https://mytodays.tistory.com/26)