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
```java
/**
 * CompletableFuture 1
 */
public class App7 {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService executorService = Executors.newFixedThreadPool(4);
        Future<String> future = executorService.submit(() -> "hello");
//        ...
        executorService.shutdown();
        System.out.println(future.get()); // future 한계는 get() 블록킹 이여서 콜백을 보장하기 힘들다

        //비동기하기 좋은 Completablefuture
        CompletableFuture<Object> future1 = new CompletableFuture<>();
        future1.complete("keesun");
        System.out.println(future1.get());

        //사용2
        CompletableFuture<String> future2 = CompletableFuture.completedFuture("keesun");
        System.out.println(future2.get());

        //사용3 void - ForkJoinPoll 사용
        CompletableFuture<Void> future3 = CompletableFuture.runAsync(() -> {
            System.out.println("Hello " + Thread.currentThread().getName());
        });
        future3.get();

        //사용4 return - ForkJoinPool 사용
        CompletableFuture<String> future4 = CompletableFuture.supplyAsync(() -> {
            System.out.println("Hello1 " + Thread.currentThread().getName());
            return "Hello";
        });
        System.out.println(future4.get());

        //사용5 return - 콜백주기(리턴있는 콜백)
        CompletableFuture<String> future5 = CompletableFuture.supplyAsync(() -> {
            System.out.println("Hello2 " + Thread.currentThread().getName());
            return "Hello1";
        }).thenApply(s -> {
            System.out.println(Thread.currentThread().getName());
            return s.toUpperCase();
        });
        System.out.println("future5.get() = " + future5.get());

        //사용6 return - 콜백주기(리턴없는 콜백)
        CompletableFuture<Void> future6 = CompletableFuture.supplyAsync(() -> {
            System.out.println("Hello3 " + Thread.currentThread().getName());
            return "Hello2";
        }).thenAccept(s -> {
            System.out.println(Thread.currentThread().getName());
            System.out.println("s.toUpperCase() = " + s.toUpperCase());
        });
        // 최종적인 값은 then 하고 호출된값이여서 리턴없는 thenAccept 이기에 null 출력
        System.out.println("future6.get() = " + future6.get());

        //사용7 return - 콜백주기(값을 받을 필요도 없다)
        CompletableFuture<Void> future7 = CompletableFuture.supplyAsync(() -> {
            System.out.println("Hello4 " + Thread.currentThread().getName());
            return "Hello3";
        }).thenRun(() -> {
            System.out.println(Thread.currentThread().getName());
        });
        System.out.println("future7.get() = " + future7.get());

        //사용8 쓰레드풀 커스텀
        ExecutorService executorService1 = Executors.newFixedThreadPool(4);
        CompletableFuture<Void> future8 = CompletableFuture.supplyAsync(() -> {
          System.out.println("Hello5 " + Thread.currentThread().getName());
          return "Hello4";
        },executorService1).thenRunAsync(() -> {
          System.out.println(Thread.currentThread().getName());
        },executorService1);
        System.out.println("future8.get() = " + future8.get());
        executorService1.shutdown();
    }
}

```

## 6-5 CompletableFuture1
- 조합하기
● thenCompose(): 두 작업이 서로 이어서 실행하도록 조합
● thenCombine(): 두 작업을 독립적으로 실행하고 둘 다 종료 했을 때 콜백 실행
● allOf(): 여러 작업을 모두 실행하고 모든 작업 결과에 콜백 실행
● anyOf(): 여러 작업 중에 가장 빨리 끝난 하나의 결과에 콜백 실행

- 예외처리
● exeptionally(Function)
● handle(BiFunction):
```java
/**
 * CompletableFuture 2
 */
public class App8 {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        // hello 결과사용해 world 호출하는경우
        CompletableFuture<String> hello = CompletableFuture.supplyAsync(() -> {
            System.out.println("Hello " + Thread.currentThread().getName());
            return "Hello";
        });
//        CompletableFuture<String> future = hello.thenCompose(s->getWorld(s));
        CompletableFuture<String> future = hello.thenCompose(App8::getWorld);
        System.out.println("future.get() = " + future.get());

        // 둘의 연관관계가 없는경우 따로따로 하는경우(실행은 동시에)
        CompletableFuture<String> hello1 = CompletableFuture.supplyAsync(() -> {
            System.out.println("Hello " + Thread.currentThread().getName());
            return "Hello";
        });
        CompletableFuture<String> World = CompletableFuture.supplyAsync(() -> {
            System.out.println("World " + Thread.currentThread().getName());
            return "World";
        });
        CompletableFuture<String> future1 = hello1.thenCombineAsync(World, (h, w) -> h + " " + w);
        System.out.println("future1.get() = " + future1.get());


        // 모든 비동기의 결과값을 컬렉션으로 받고 싶을때 ( join 은 UnCheckedException 을 발생시켜 에러가 나지 않는다. )
        List<CompletableFuture<String>> futures = Arrays.asList(hello1, World);
        CompletableFuture[] futuresArray = futures.toArray(new CompletableFuture[futures.size()]);
        CompletableFuture<List<String>> results = CompletableFuture.allOf(futuresArray)
                .thenApply(v -> {
                    return futures.stream()
                            .map(f -> f.join())
                            .collect(Collectors.toList());
                });
        results.get().forEach(System.out::print);

        System.out.println();
        // 아무거나 하나 빨리끝난 비동기들의 첫번째 결과를 가져올때
        CompletableFuture<Void> future2 = CompletableFuture.anyOf(futuresArray)
                .thenAccept(System.out::println);
        future2.get();

        // 예외처리 .exceptionally
        boolean throwError = true;
        CompletableFuture<String> future3 = CompletableFuture.supplyAsync(() -> {
            if (throwError) {
                throw new IllegalArgumentException();
            }
            System.out.println("Hello " + Thread.currentThread().getName());
            return "HELLO!";
        }).exceptionally(ex -> {
            return "Error!";
        });
        System.out.println(future3.get());

        // 예외처리 .handle
        boolean throwError2 = true;
        CompletableFuture<String> future4 = CompletableFuture.supplyAsync(() -> {
            if (throwError2) {
                throw new IllegalArgumentException();
            }
            System.out.println("Hello " + Thread.currentThread().getName());
            return "HELLO@";
        }).handle((result, error) -> {
            if (error != null) {
                return "ERROR@";
            }
            return result;
        });
        System.out.println(future4.get());
    }

    private static CompletableFuture<String> getWorld(String message) {
        return CompletableFuture.supplyAsync(() -> {
            System.out.println("World " + Thread.currentThread().getName());
            return  message+" World";
        });
    }
}

```
