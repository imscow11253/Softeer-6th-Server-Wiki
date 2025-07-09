# 📚 버전별 Thread 모델 학습

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

1. Callable </br>
Thread가 종료되었을 때 값을 return하고 이를 받아볼 수 있도록 만든 클래스
2. Future
3. Executor (Interface)
4. ExecutorService (Interface)
5. ScheduledExecutorService (Interface)
6. Executors (팩토리 클래스)

https://mangkyu.tistory.com/259