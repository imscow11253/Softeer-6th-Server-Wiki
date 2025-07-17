# 📚 JVM 파헤치기

7.16 오전 수업으로 jvm에 대해서 학습했다. 
짧은 시간 동안 조사를 빠르게 하고 팀원들끼리 서로 설명해가는 방식으로 공부를 진행했고,
학습한 내용을 정리하려고 한다. 추가적으로 학습한 내용도 같이 적어보려고 한다. 

## JDK vs JRE vs JVM
java를 실행하기 위한 도구들의 모음이라는 점에서 동일하지만 도구들의 포함 범위에 따라 나뉜다.
도식도로 표현하면 다음과 같은 구조를 가진다. 
```text
+-------------------------------------------------------------+
|                            JDK                              |
|  +---------------------------------+  +-------------------+ |
|  |              JRE                |  | Java Development  | |
|  |                                 |  |      Tools        | |
|  |  +--------+   +---------------+ |  |-------------------| |
|  |  |  JVM   |   | Java Class    | |  | javac             | |
|  |  |        |   | Library       | |  | java              | |
|  |  +--------+   +---------------+ |  | javap             | |
|  |                                 |  | apt               | |
|  +---------------------------------+  | jar               | |
|                                       | jdb               | |
|                                       | javadoc           | |
|                                       | ...               | |
|                                       +-------------------+ |
+-------------------------------------------------------------+
```
- JDK 
  - Java Development Kit의 약자로, 자바 파일을 실행하기 위한 모든 툴들이 모여있는 패키지이다.
  - jre이 포함된다. 
  - javac : 바이트 코드 변환기, 자바 컴파일러
  - 등
- JRE
  - Java Runtime Environment의 약자로, 자바 라이브러리와 jvm을 포함한다.
- JVM
  - Java Virtual Machine의 약자로, 실질적으로 자바 바이트 코드가 실행되는 환경이다. 
  - 내부적으로 크게 class loader, runtime data area, execution engine 으로 나뉜다. 

## JVM에 대해서 
```text
HotSpot JVM: Architecture

           +-------------------------------+
Class Files|      Class Loader Subsystem   |
---------->|   (클래스 로딩 서브시스템)    |
           +-------------------------------+
                          |
                          v
   +-------------------------------------------------------------+
   |                       Runtime Data Areas                    |
   |  +-------------+  +------+  +-------------+  +-------------+  +------------------------+ |
   |  | Method Area |  | Heap |  | Java Threads|  | Program Counter |  | Native Internal Threads | |
   |  +-------------+  +------+  |             |  |   Registers    |  +------------------------+ |
   +-------------------+--------+--------------+-------------------+----------------------------+
                          |
                          v
   +-------------------------------------------------------------+
   |                    Execution Engine                         |
   | +-------------+ +--------------+ +-----------------------+  |
   | | Execution   | | JIT Compiler | |   Garbage Collector   |  |
   | |   Engine    | +--------------+ +-----------------------+  |
   +-------------+------------------------------------------------+
                          ^
                          |
        +-----------------+------------------+
        |                                   |
        |     +-------------------------+    |
        +---> | Native Method Interface | <---+
              +-------------------------+
                        ^
                        |
          Native Method Libraries
```

- Class Loader 
  - 컴파일된 자바 코드(바이트 코드 .class 확장자)를 jvm 메모리 영역에 올리는 역할을 한다. 
- Runtime Data Area
  - jvm의 메모리 영역으로, pc의 main memory를 담당한다. 
  - method, heap, stack, pc register 등으로 나뉜다. 
- Execution Engine
  - 실질적으로 자바 바이트 코드를 실행한다. 
  - 기본적으로 인터프리터 방식으로 동작하지만, 최적화를 위해 JIT 컴파일러를 사용하기도 한다. 
  - JIT : Just-in-time의 약자로, 반복해서 등장하는 코드나 최적화가 가능한 부분들을 컴파일 해두어서 실행시간을 줄이는 역할을 한다.
  - GC : 가비지 컬렉터의 약자로, 런타임 시에 주기적으로 heap, method 영역을 탐색하고 더이상 쓰이지 않는 객체에 대해서 메모리를 free 한다. 

## Hello World 실행 흐름
1. 개발자가 Hello World를 출력하는 자바 코드를 짠다. (.java 파일)
2. 자바 컴파일러 (javac)가 .java 확장자의 파일을 컴파일 해서 바이트 코드 (.class 확장자)를 생성한다. 
3. Class Loader가 해당 바이트 코드를 runtime data area에 적재한다. 
4. Execution Engine이 메모리에 적재된 바이트 코드를 실행한다. 

## 런타임 메모리 영역
jvm의 메모리 영역은 면접 단골 질문이다. 알아두도록 하자. 
1. method 영역
   - class에서 한 번만 생성되는 부분이 저장된다. (method, static 변수/메소드, 클래스 메타데이터)
   - 모든 스레드가 참조가능하다. 
2. heap 영역
   - class의 객체들이 생성되는 곳이다. 
   - 모든 스레드가 참조가능하다. 
   - GC의 대상이다. 
   - java 8 이후부터 컨스턴트 풀도 heap에 위치한다. (= 컨스턴트 풀도 GC의 대상이 되었다.)
3. stack 영역
   - 스레드 별로 영역을 할당 받는다. 
   - 메소드가 호출될 때마다 stack frame이 선입후출의 구조로 쌓인다.
     - stack frame에는 파라미터, 로컬 변수, 리턴 값 등이 들어간다.
   - 스레드 영역 간 참조는 불가능하다.
   - 스레드가 종료되면 스레드 영역도 없어진다. 
4. pc register
   - 각 스레드 별 context switching이 일어날 때 필요한 pc register를 가진다.

## GC가 heap을 탐색하는 과정
GC에는 heap을 어떻게 탐색하고, 객체를 어떻게 free할 것인지 성격, 역할에 따라 종류가 나뉜다. 
일단 기본적인 GC의 동작을 보자. 

heap은 크게 2가지 영역으로 나뉜다. young-generation, old-generation

객체가 처음 생성되면 young-generation 공간에 할당된다. 
young-generation이 가득 차거나, GC의 주기가 되었을 때 GC는 heap 영역을 순회하며 참조되지 않는 객체가 있는지 탐색한다. 
있다면 해당 메모리 공간을 free 시켜주고, 없다면 내버려둔다. 

만약 GC의 검사(?)로부터 살아남은 객체는 오래 살아남을수록 old-generation 공간으로 이동된다. 
오래 살아남았다는 것은 앞으로도 오래 참조될 것이라고 판단하고 GC의 관리 대상에서 우선순위를 적게 주는 것이다. 

- mark
  - 접근 가능한 객체에 대해서 표시를 해두는 것
- sweep
  - mark 되지 않은 객체의 메모리 공간을 free 시키는 것
- compact
  - 메모리 단편화가 발생했을 때 객체의 메모리 영역을 한 쪽으로 정리하는 것

## GC의 종류 
- Serial GC
  - GC를 위한 스레드가 하나이고, 해당 스레드가 동작하는 동안 다른 작업을 all stop이다. 
- Parallel GC
  - GC를 위한 스레드가 여러개이고, GC가 돌아가는 동안 병렬적으로 동작한다. 다른 작업은 all-stop
- CMS GC
  - mark 하는 단계를 세분화해서 stop-the-world 되는 상황을 줄인 것
- G1 GC
  - heap 영역을 일정 단위의 region으로 나누어서 old/young을 유동적으로 관리하는 것
- Z GC
  - heap 영역을 유동 단위의 region으로 나누어서 old/young을 유동적으로 관리하는 것

## GC 설정하기 
자바를 실행할 때 option 으로 GC를 지정할 수 있다. 
```text
java -jar -XX:+UseSerialGC Main.java    // Serial GC
java -jar -XX:+UseParallelGC Main.java  // Parallel GC
java -jar -XX:+UseG1GC Main.java        // G1 GC
java -jar -XX:+UseZGC Main.java         // Z GC
```