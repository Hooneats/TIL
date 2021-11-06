# 3. Stream

## 3-1 Stream 소개
스트림으로 처리하는 데이터는 오직 한번만 처리한다.
스트림이 처리하는 데이터 소스를 변경하지 않는다.(Functional in nature)
- 중개 오퍼레이션
Stream 을 리턴한다.
filter, map, limit, skip, sorted ...
- 종료 오퍼레이션
Stream 을 리턴하지 않는다.
collect, allMatch, count, forEach, min, max ...

## 3-2 스트림 API - OnlineClass.java , App2.java
```java
package java8to11.stream;

public class OnlineClass {

    private Integer id;

    private String title;

    private boolean closed;

    public OnlineClass(Integer id, String title, boolean closed) {
        this.id = id;
        this.title = title;
        this.closed = closed;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public boolean isClosed() {
        return closed;
    }

    public void setClosed(boolean closed) {
        this.closed = closed;
    }
}

```
```java
package java8to11.stream;

import java.util.ArrayList;
import java.util.Collection;
import java.util.List;
import java.util.function.Predicate;
import java.util.stream.Collectors;
import java.util.stream.Stream;

/**
 * 자바 8 Stream API
 * */
public class App2 {

    public static void main(String[] args) {
        List<OnlineClass> springClasses = new ArrayList<>();
        springClasses.add(new OnlineClass(1, "spring boot", true));
        springClasses.add(new OnlineClass(2, "spring data jpa", true));
        springClasses.add(new OnlineClass(3, "spring mvc", false));
        springClasses.add(new OnlineClass(4, "spring core", false));
        springClasses.add(new OnlineClass(5, "rest api development", false));

        List<OnlineClass> javaClasses = new ArrayList<>();
        javaClasses.add(new OnlineClass(6, "The java, test", true));
        javaClasses.add(new OnlineClass(7, "The java, Code manipulation", true));
        javaClasses.add(new OnlineClass(8, "The java, 8 to 11", false));

        List<List<OnlineClass>> keesunEvents = new ArrayList<>();
        keesunEvents.add(springClasses);
        keesunEvents.add(javaClasses);

        System.out.println("spring 으로 시작하는 수업");
        // TODO
        // 나의 답안
        List<String> spring1 = springClasses.stream().map(s -> {
            boolean spring = s.getTitle().toLowerCase().startsWith("spring");
            if (spring) return s.getTitle();
            return null;
        }).collect(Collectors.toList());
        spring1.forEach(System.out::println);
        // spring boot, spring data jpa, spring mvc, spring core, null, null, null,null

        // 기선님 답안
        springClasses.stream().filter(oc -> oc.getTitle().startsWith("spring"))
                .forEach(oc -> System.out.println("oc.getTitle() = " + oc.getTitle())); // 1안
        List<String> spring = springClasses.stream().filter(oc -> oc.getTitle().startsWith("spring"))
                .map(oc -> oc.getTitle()).collect(Collectors.toList()); // 2안
        spring.forEach(System.out::println);


        System.out.println("close 되지 않은 수업");
        // TODO
        // 나의 답안
        keesunEvents.stream().forEach(ocL -> ocL.stream().filter(oc -> !oc.isClosed())
                .forEach(oc -> System.out.println("oc.getTitle() = " + oc.getTitle())));
        // 기선님 답안
        springClasses.stream().filter(oc->!oc.isClosed())
                        .forEach(oc-> System.out.println("oc = " + oc.getTitle())); // 1안
        springClasses.stream().filter(Predicate.not(OnlineClass::isClosed))
                .forEach(oc-> System.out.println("oc = " + oc.getTitle())); // 2안


        System.out.println("수업 이름만 모아서 스트림 만들기");
        // TODO
        // 나의 답안
        List<String> collect = springClasses.stream().map(oc -> oc.getTitle()).collect(Collectors.toList());
        collect.stream().forEach(System.out::println);
        // 기선님 답안
        springClasses.stream().map(oc->oc.getTitle())
                        .forEach(System.out::println);

        System.out.println("두 수업 목록에 들어있는 모든 수업 아이디 출력");
        // TODO
        // 나의 답안
        keesunEvents.stream().forEach(ocL->ocL.stream().map(OnlineClass::getTitle)
                .forEach(System.out::println));
        // 기선님 답안
//        keesunEvents.stream().flatMap(list -> list.stream())
        keesunEvents.stream().flatMap(Collection::stream)
                        .forEach(oc-> System.out.println("oc.getTitle() = " + oc.getTitle()));

        System.out.println("10부터 1씩 증가하는 무제한 스트림 중에서 앞에 10개 빼고 최대 10개 까지만");
        // TODO
        // 기선님 답안
        Stream.iterate(10, i -> i + 1) // 10부터 1씩 증가하는 무제한 스트림
                .skip(10)
                .limit(10)
                .forEach(System.out::println);

        System.out.println("자바 수업 중에 Test가 들어있는 수업이 있는지 확인");
        // TODO
        // 나의 답안
        javaClasses.stream().filter(oc -> oc.getTitle().toLowerCase().contains("test"))
                .forEach(oc -> System.out.println("oc.getTitle() = " + oc.getTitle()));
        // 기선님 답안
        boolean test = javaClasses.stream().anyMatch(oc -> oc.getTitle().contains("test"));
        System.out.println("test = " + test);

        System.out.println("스프링 수업 중에 제목에 spring이 들어간 제목만 모아서 List로 만들기");
        // TODO
        // 나의 답안
        List<OnlineClass> spring2 = springClasses.stream().filter(oc -> oc.getTitle().toLowerCase().contains("spring"))
                .collect(Collectors.toList());
        for (OnlineClass onlineClass : spring2) {
            System.out.println("onlineClass.getTitle() = " + onlineClass.getTitle());
        }
        // 기선님 답안
        springClasses.stream().filter(oc -> oc.getTitle().contains("spring"))
                .map(OnlineClass::getTitle)
                .collect(Collectors.toList());
        springClasses.stream().map(OnlineClass::getTitle)
                .filter(title -> title.contains("spring"))
                .collect(Collectors.toList());
    }
}

```