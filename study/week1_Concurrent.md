# 📚 Java의 Concurrent 패키지 학습

25.7.9

## Intro

concurrent는 Thread의 생성과 관리, 동시성 제어를 위한 라이브러리이다.

## Thread 생성 및 Thread pool
Thread를 생성하기 위한 Runnable, Callable, Executor, Future, CompletableFuture 등이 정의되어 있다. </br>
week1_Thread.md 파일에 정리했던 클래스, 인터페이스들이 java.utils.concurrent 패키지 안에 존재한다. </br>
자세한 클래스, 인터페이스 내용은 week1_Thread.md 문서를 참고하자. 

## Thread 동시성 제어
자바에서 생성되는 모든 객체는 Lock이 기본적으로 존재한다. </br>
특정 객체나 메소드를 대상으로 해당 락을 사용할 수 있는데, 이때 사용되는 것이 synchrozied 키워드이다. 

객체가 아닌 명시적으로 락을 사용하고 싶으면 concurrent 패키지에 Lock 인터페이스, ReentrantLock 클래스를 사용하면 된다.</br>
그 외에도 다양한 기능, 성격을 가진 Lock 구현체가 제공된다. 

## Concurrent Collections
멀티 스레드 환경에서 여러 스레드가 하나의 자료구조에 접근할 때, Race condition을 방지하기 위해서 락을 사용한다. </br>
근데 자료구조 하나하나마다 lock 로직을 사용하면 귀찮다. </br></br>

concurrent 패키지에서는 이런 동시성 문제를 해결한 Collection을 제공해준다. </br>
그러면 개발자는 명시적으로 lock으로 동시성 제어를 할 필요 없이, 추상화된 메소드를 사용하면 동시성을 알아서 제어된다. </br>

- ConcurrentHashMap
- CopyOnWriteArrayList
- ConcurrentLinkedQueue
- ...

필요할 떄마다 찾아서 쓰자.

