# 6. CompletableFuture

## 6-1 자바 Concurrent 프로그래밍 소개
### Concurrent 소프트웨어란 동시에 여러 작업을 할 수 있는 소프트웨어이다.

- 자바에서 지원하는 컨커런트 프로그래밍
멀티프로세싱(ProcessBuilder) , 멀티쓰레드

## 6-2 Executor
### 쓰레드를 만들고 관리하는 작업을 Executor 에게 위임
Executor 를 상속받고 있는 ExecutorService 가 있고 ExecutorService 를 상속받고 있는 ScheduledExecutorService 가 있다.
### Runnable 은 리턴타입을 갖고있지않은 void 여서 값을 가져올수 없다.
### 이때 쓰이는게 Callable 이고 , 이는 리턴타입을 갖고있고 이 때 Future 를 사용한다.
```java
/**
 * Runnable
 * */
public class App5 {
    public static void main(String[] args) {
        // ExecutorService 는 명시적으로 닫아줘야한다.
        ExecutorService executorService = Executors.newSingleThreadExecutor();
        // execute()는 작업 처리 동중에 예외가 발생하면 스레드가 종료되고 해당 스레드를 스레드풀에서 제거한 뒤, 다른 작업처리를 위해서 새로운 스레드를 생성한다
        executorService.execute(()->{
            System.out.println("App5.main.Thread.execute : " + Thread.currentThread().getName());
        });
        // submit()는 작업처리 도중에 예외가 발생하더라도 스레드는 종료되지 않고 다음 작업을 위해 재사용된다.
        executorService.submit(()->{
            System.out.println("App5.main.Thread.submit : " + Thread.currentThread().getName());
        });
        // 스레드 생성 오버헤더를 줄이기 위해서 submit()을 사용하는 것이 좋다.
        // 명시적으로 쓰레드를 닫아준다. => .shutdown() 은 작업을 마치면 종료시키고, .shutdownNow() 는 작업이 실행중이더라도 바로 종료시킨다.
        executorService.shutdown();

        System.out.println();

        ExecutorService executorService1 = Executors.newFixedThreadPool(2);
        executorService1.submit(getRunnable("Hello"));
        executorService1.submit(getRunnable("Hello1"));
        executorService1.submit(getRunnable("Hello2"));
        executorService1.submit(getRunnable("Hello3"));
        executorService1.submit(getRunnable("Hello4"));
        executorService1.submit(getRunnable("Hello5"));
        executorService1.submit(getRunnable("Hello6"));
        executorService1.submit(getRunnable("Hello7"));
        executorService1.submit(getRunnable("Hello8"));
        executorService1.submit(getRunnable("Hello9"));
        executorService1.shutdown();

        ScheduledExecutorService executorService2 = Executors.newSingleThreadScheduledExecutor();
        // 3초 정도 있다가 실행해라
        executorService2.schedule(getRunnable("HelloSchedule1"), 3, TimeUnit.SECONDS);
        // 1초 기다렸다가 2초에 한번씩 실행해라
        executorService2.scheduleAtFixedRate(getRunnable("HelloRepeat"),1,2, TimeUnit.SECONDS);
        executorService2.shutdown();
    }

    private static Runnable getRunnable(String message) {
        return () -> {
            System.out.println(message + Thread.currentThread().getName());
        };
    }
}

```

---

## 6-3 Callable 과 Future
Callable 은 리턴값을 가질 수 있다.
```java
/**
 * Callable
 * */
public class App6 {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService executorService = Executors.newSingleThreadExecutor();
//        Callable<String> stringCallable = new Callable<>() {
//            @Override
//            public String call() throws Exception {
//                return null;
//            }
//        };
        Callable<String> hello = () -> {
            Thread.sleep(2000L);
            return "Hello";
        };
        Future<String> submit = executorService.submit(hello);
        System.out.println("기다리지않고 코드가 실행되다 get() 을 만나는 순간 기다린다.");
        // 작업 상태 확인
        System.out.println(submit.isDone()); // 끝났으면 true , 안끝났으면 false
        System.out.println("Started");

        // 쓰레드 취소 이때 true 를 매개변수로 하든 false 로 하든 일단 cancel 를 하면 Done 은 true 가 되고 값은 가져올수없다.
        // cancel 이 true 든 false 든 일단 호출했는데 get() 하려 하면 CancellationException 이 발생한다. - 앱 종료되지않는다.
//        submit.cancel(false);

        submit.get();// 블록킹 (쓰레드가 끝나 값이 나올때까지 기다림)
        System.out.println(submit.isDone()); // 끝났으면 true , 안끝났으면 false

        System.out.println("End");
        executorService.shutdown();

        ExecutorService executorService1 = Executors.newFixedThreadPool(3);
        Callable<String> hello1 = () -> {
            Thread.sleep(2000L);
            return "Hello1";
        };
        Callable<String> java = () -> {
            Thread.sleep(3000L);
            return "java";
        };
        Callable<String> keesun = () -> {
            Thread.sleep(1000L);
            return "keesun";
        };
        // Callable 들을 한번에 실행시켜 한번에 리스트로 결과값을 가져올수 있다. -> 이때 Callable 모두가 끝날때까지 기다렸다가 가져온다.
        List<Future<String>> futures = executorService1.invokeAll(Arrays.asList(hello1, java, keesun));
        for (Future<String> future : futures) {
            System.out.println("future.get() = " + future.get());
        }
        // Callable 들을 한번에 실행시켜 한번에 리스트로 결과값을 가져올수 있다. -> 이때 가장 빨리끝난 결과를 가져온다.
        String s = executorService1.invokeAny(Arrays.asList(hello1, java, keesun));
        System.out.println("s = " + s);

        executorService1.shutdown();
    }
}
```

---

## 6-4 CompletableFuture1
### 자바에서 비동기(Asynchronous) 프로그래밍을 가능케하는 인터페이스
- Future 의 단점
  ● Future를 외부에서 완료 시킬 수 없다. 취소하거나, get()에 타임아웃을 설정할 수는 있다.
  ● 블로킹 코드(get())를 사용하지 않고서는 작업이 끝났을 때 콜백을 실행할 수 없다.
  ● 여러 Future를 조합할 수 없다, 예) Event 정보 가져온 다음 Event에 참석하는 회원 목록 가져오기
  ● 예외 처리용 API를 제공하지 않는다.

- CompletableFuture
  ● Implements Future
  ● Implements CompletionStage

- 비동기로 작업 실행하기 -> 기본적으로 ForkJoinPool 를 사용하지만 두번째 인자로 우리의 쓰레드풀로 교체도 가능
  ● 리턴값이 없는 경우: runAsync() ---- 이때 두번째 인자로 우리가 쓰레드풀로 만든 쓰레드도 사용가능
  ● 리턴값이 있는 경우: supplyAsync() ---- 이때 두번째 인자로 우리가 쓰레드풀로 만든 쓰레드도 사용가능
  ● 원하는 Executor(쓰레드풀)를 사용해서 실행할 수도 있다. (기본은 ForkJoinPool.commonPool())

- 콜백 제공하기
  ● thenApply(Function): 리턴값을 받아서 다른 값으로 바꾸는 콜백
  ● thenAccept(Consumer): 리턴값을 또 다른 작업을 처리하는 콜백 (리턴없이)
  ● thenRun(Runnable): 리턴값 받지 다른 작업을 처리하는 콜백
  ● 콜백 자체를 또 다른 쓰레드에서 실행할 수 있다.
- 콜백또한 ~~~Async() 로 사용해서 두번째인자로 우리가 쓰레드풀로 만든 쓰레드도 사용가능
