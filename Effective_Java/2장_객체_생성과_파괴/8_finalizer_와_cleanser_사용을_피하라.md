## finalizer 와 cleanser 사용을 피하라.

### finalizer 는 예측할 수 없고, 상황에 따라 위험할 수 있어 일반적으로 불필요하다.

자바 9에서는 finalizer를 사용 자제 API(deprecated) 로 지정하고 cleanser 를 그 대안으로 소개하고 있다.

### 그러나 cleaner 는  finalizer 보다는 덜 위험하지만, 여전히 예측할 수 없고,  느리고 , 일반적으로 불 필요하다.

finalizer 와 cleaner 는 즉시 수행된다는 보장이 없다. 즉, finalizer 와 cleaner 로는 제때 실행되어야 하는 작업은 절대 할 수 없다.
예컨대, 파일 닫기를 `finalizer` 나 `cleaner` 에 맡기면 중대한 오류를 일으킬 수 있다.
이는 테스트한 JVM 에서는 완벽하게 동작하던 프로그램이 가장 중요한 고객의 시스템에서는 엄청난 재앙을 일으킬지도 모른다.
```
불행히도 finalizer 스레드는 다른 애플리케이션 스레드보다 우선순위가 낮아서 실행될 기회를 제대로 얻지 못한 것이다.
한편, cleaner는 자신을 수행할 스레드를 제어할 수 있다는 면에서 조금 더 낫지만, 즉각 수행되리라 보장되지 않는다.
수행 시점뿐 아니라 수행 여부조차 보장하지 않는다.
```

### 상태를 영구적으로 수정하는 작업에서는 절대 finalizer 나 cleaner 에 의존해서는 안된다.
예를 들어 데이터베이스 같은 공유 자원의 영구 락(lock) 해제를 finalizer 나 cleaner 에 맡겨 놓으면 분산 시스템 전체가 서서히 멈출 것이다.

### System.gc 나 System.runFinalization 메서드에 현혹되지 말자.
finalizer 와 cleaner 가 실행될 가능성을 높여줄 수는 있으나, 보장해주진 않는다.

---

### finalizer 를 사용한 클래스는 finalizer 공격에 노출되어 심각한 보안 문제를 일으 킬 수 있다.
생성자나 직렬화 과정에서 예외가 발생하면, 이 생성되다 만 객체에서 악의적인 하위 클래스의 finalizer 가 수행될 수 있게 된다.
이는 있어서는 안 될 일이다. 
 
### 참고 블로그 :

### [Finalizer attack](https://yangbongsoo.tistory.com/8?category=919799)

---

### finalizer 나 cleaner 를 대신해줄 묘안은 무엇일까? 
### AutoCloseable 을 구현해주고, 클라이언트에서 인스턴스를 다 쓰고 나면 close 메서드를 호출하면 된다.
### (일반적으로 예외가 발생해도 제대로 종료되도록 try-whit-resources 를 사용해야 한다.)
이떄 각 인스턴스는 자신이 닫혔는지를 추적하는 것이 좋다. close 메서드에서 이 객체는 더이상 유효하지 않음을 필드에 기록하고,
다른 메서드는 이 필드를 검사해서 객체가 닫힌 후에 불렸다면 IllegalStateException 을 던지는 것이다.
```java
public class SampleResource implements AutoCloseable {
    private boolean closed;

    @Override
    public void close() throws RuntimeException {
        if(this.closed) throw new IllegalStateException();
        closed = true;
        System.out.println("close");
    }
    
    public void hello() {
        System.out.println("hello");
    }
}
```
```java
public class SampleRunner {
    public static void main(String[] args) {
        try(SampleResource sampleResource = new SampleResource()) {
            sampleResource.hello();
        }
    }
}
```
#### 이때 finalizer 를 안전망으로 둘 수 있다. 이렇게 안전망으로 finalizer 를 둔 자바 라이브러리의 일부 클래스는 대표적으로
#### `FileInputStream` , `FileOutputStream` , `ThreadPoolExecutor` 가 대표적이다.

```java
public class SampleResource implements AutoCloseable {
    private boolean closed;

    @Override
    public void close() throws RuntimeException {
        if(this.closed) throw new IllegalStateException();
        closed = true;
        System.out.println("close");
    }
    
    public void hello() {
        System.out.println("hello");
    }
    /**
     * 여기 finalizer 를 안전망으로 둘 수 있다.
     * */
    @Override
    protected void finalizer() throws Throwable {
        if(!this.closed) closed();
    }
}
```
```java
public class SampleRunner {
    public static void main(String[] args) {
        try(SampleResource sampleResource = new SampleResource()) {
            sampleResource.hello();
        }
    }
}
```

### 참고 영상 : (14:00~)

### [[이팩티브 자바] #8 Finalizer와 Cleaner 쓰지 마세요](https://youtu.be/sdPdpMYqW_k)

### 참고 블로그 :

### [[이펙티브 자바] 아이템 8. finalizer와 cleaner 사용을 피하라](https://velog.io/@lychee/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%EC%9E%90%EB%B0%94-%EC%95%84%EC%9D%B4%ED%85%9C-8.-finalizer%EC%99%80-cleaner-%EC%82%AC%EC%9A%A9%EC%9D%84-%ED%94%BC%ED%95%98%EB%9D%BC)

### [아이템 8. finalizer와 cleaner 사용을 피하라](https://velog.io/@banjjoknim/%EC%95%84%EC%9D%B4%ED%85%9C-8.-finalizer%EC%99%80-cleaner-%EC%82%AC%EC%9A%A9%EC%9D%84-%ED%94%BC%ED%95%98%EB%9D%BC)

### [[이펙티브자바] finalizer와 cleaner 사용을 피하라](https://jgrammer.tistory.com/entry/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C%EC%9E%90%EB%B0%94-finalizer%EC%99%80-cleaner-%EC%82%AC%EC%9A%A9%EC%9D%84-%ED%94%BC%ED%95%98%EB%9D%BC)

### [아이템 8: Finalizer와 Cleaner는 피하라](https://github.com/keesun/study/blob/master/effective-java/item8.md)




