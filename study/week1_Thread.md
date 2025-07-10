# 📚 버전별 Thread 모델 학습

25.7.7

## 자바에서의 Thread
자바는 jvm 위에서 동작한다. 즉 우리가 thread를 생성하더라도 시스템 콜을 직접 호출하는 것이 아니다. jvm이 시스템 콜을 호출하고 우리가 생성한 Thread와 mapping 해준다.

## Thread 생성법 at java 5 이전
1. Thread 클래스를 상속 후 run 메소드 오버라이딩
2. Runnable 인터페이스의 run 메소드 구현 후 Thread 생성자로 전달

💁‍ Thread 객체 생성 후 run 메소드를 호출하면 그 객체의 메소드를 호출한 형태가 된다. </br>
jvm이 시스템 콜을 호출하도록 하는 것이 목표이기 떄문에 start 메소드를 호출해야 한다. </br>

https://mangkyu.tistory.com/258

## Thread 생성법 at java 5
이전의 방식은 thread 생성이 복잡하고, 개발자가 thread를 어떻게 생성해야 하는지 알아야 한다는 점에서 service 로직 구현에만 집중할 수 없게 된다. </br>
비용도 많이 들고, thread 관리도 어렵다. 또, thread의 값을 반환 받을 수 없다는 단점이 있다.

1. Callable (class) </br>
스레드가 종료되었을 때 값을 리턴하고 이를 받아볼 수 있도록 만든 클래스. 
2. Future (class) </br>
미래의 완료된 스레드에 한해서만 값을 받아볼 수 있게 스레드 종료 시점을 추상화한 클래스. </br>
아직 스레드가 종료 안되었는데, 사용자가 리턴값을 받아보고 싶을 때 
3. Executor (Interface) </br>
스레드 풀을 생성하고 들어온 Runnable/Callable을 스레드에 할당하기 위한 인터페이스. </br>
execute 메소드를 구현해야 한다. 
4. ExecutorService (Interface) </br>
Runnable/Callable을 등록하기 위한 인터페이스. Executor를 상속 받기에 등록, 실행까지 한다.  
5. ScheduledExecutorService (Interface) </br>
특정 시간, 주기적으로 작업을 수행해야 하는 경우 사용되는 스레드 풀 인터페이스.
6. Executors (팩토리 클래스) </br>
필요한 성격에 따른 스레드 풀이 static 구현체로 만들어져 있어서 호출해서 사용하면 된다. 
   - newFixedThreadPool : thread pool에 생성될 thread 개수를 고정
   - newSingleThreadExecutor : 스레드 하나, 싱글톤처럼 사용
   - newCachedThreadPool : max 개수만 지정하고, 유동적으로 thread 생성,삭제
   - newScheduledThreadPool : 스케줄링된 작업을 실행

ex ) 
```java
ExecutorService executorService = Executors.newFixedThreadPool(10);
```

https://mangkyu.tistory.com/259

## Thread 생성법 at java 8

기존 Future 클래스는 블록킹 메소드인 get으로만 값을 가져올 수 있어서, 항상 동기식으로 동작한다는 단점이 있다. 
그래서 여러 작업 (Future)들을 조합할 수 없었다. --> 처음거 기다리고, 다음거 기다리고... </br>
또한 타임아웃 시간 설정만 가능하지, 외부에서 작업을 완료시킬 수단이 없다. 

1. CompletableFuture </br>
외부에서 작업을 완료시킬 수 있을 뿐만 아니라 콜백 등록 및 Future 조합 등이 가능. </br>
   - 비동기 작업 실행 
     - runAsync
     - supplyAsync
   - 작업 콜백
     - thenApply
     - thenAccept
     - thenRun
   - 작업 조합
     - thenCompose 
     - thenCombine
     - allOf
     - anyOf
   - 예외 처리
     - exceptionally
     - handle, handleAsync

이런 메소드들이 있다는 것만 알고, 사용할 때 찾아서 하자.

https://mangkyu.tistory.com/263