# 4. Optional

## 4-1 Optional 소개
- 오직 한개의 값 이 들어있을 수도 없을 수도 있는 컨테이너

### 주의할것
- 리턴값으로만 쓰기를 권장한다. (메소드 매개변수 타입, 맵의 키타입, 인스턴스 필드 타입으로는 쓰지 말자!)
- Optional 을 리턴하는 메소드에서 null 을 리턴하지 말자( 값이 없음을 리턴할 때는 Optional.empty() 를 쓰는게 낫다 )
- 프리미티브 타입용 Optional 은 따로 있다! OptionalInt, OptionalLong ... 
- Collection, Map, Array, Optional 은 이미 null 을 표현 할 수 있다. 즉 이들은 Optional 로 감싸지 말자!

---

## 4-2 Optional API
```java
public class App3 {

    public static void main(String[] args) {
        List<OnlineClass2> springClasses = new ArrayList<>();
        springClasses.add(new OnlineClass2(1, "spring boot", true));
        springClasses.add(new OnlineClass2(2, "spring data jpa", true));
        springClasses.add(new OnlineClass2(3, "spring mvc", false));
        springClasses.add(new OnlineClass2(4, "spring core", false));
        springClasses.add(new OnlineClass2(5, "rest api development", false));

        // Optional 을 리턴하는 .findFirst()
        Optional<OnlineClass2> spring = springClasses.stream()
                .filter(oc -> oc.getTitle().startsWith("spring"))
                .findFirst();
        boolean present = spring.isPresent();
        boolean empty = spring.isEmpty();
        System.out.println("present = " + present); // true
        System.out.println("empty = " + empty); // false
        OnlineClass2 onlineClass2 = spring.get();
        System.out.println("OnlineClass2.getTitle() = " + onlineClass2.getTitle());

        Optional<OnlineClass2> jpa = springClasses.stream()
                .filter(oc -> oc.getTitle().startsWith("jpa"))
                .findFirst();
        
        // Optional 의 if
        jpa.ifPresent(System.out::println);
        jpa.ifPresentOrElse(System.out::println,()->{
            System.out.println("값이 없습니다.");
        });

        // orElse 는 값이 있어도 createNewClass() 를 한번은 호출한다.
        OnlineClass2 onlineClass21 = jpa.orElse(createNewClass());
        System.out.println("onlineClass21.getTitle() = " + onlineClass21.getTitle());

        // orElseGet 은 값이 있으면 createNewClass() 를 호출하지 않는다. 이는 Supplier 함수형 인터페이스를 활용하기에 Lazy 한 방식으로 동작해서이다.
        jpa.orElseGet(()->createNewClass());
        jpa.orElseGet(App3::createNewClass);

        /**
        OnlineClass2 onlineClass22 = jpa.orElseThrow();// 기본적으로는 NoSuchElementException 을 던진다.
        System.out.println("onlineClass22.getTitle() = " + onlineClass22.getTitle());
        OnlineClass2 onlineClass23 = jpa.orElseThrow(() -> new IllegalStateException());
        OnlineClass2 onlineClass24 = jpa.orElseThrow(IllegalAccessError::new);
        System.out.println("onlineClass23.getTitle() = " + onlineClass23.getTitle());
        System.out.println("onlineClass24.getTitle() = " + onlineClass24.getTitle());
         */
        
        // Optional 은 .filter() , .map() 가능하다.
        Optional<String> spring1 = jpa.filter(oc -> oc.getTitle().startsWith("spring")).map(onlineClass22 -> onlineClass22.getTitle());
        System.out.println("spring1.orElse(null).getTitle() = " + spring1.orElse("값이 없습니다."));
        Optional<Integer> integer = jpa.map(OnlineClass2::getId);
        System.out.println("integer.orElse(0) = " + integer.orElse(0));

        //추가적으로 .flatMap() 또한 가능하다.
    }

    private static OnlineClass2 createNewClass() {
        System.out.println("App3.createNewClass");
        return new OnlineClass2(10, "new class", false);
    }

}

```