# 웹 서버 1단계 - index.html 응답

작성자 - 권민혁

## ✅ 할 일
- [x] 기존 코드 분석
- [x] 자바 버전별 Thread 모델 학습
- [x] 자바 Concurrent 패키지 학습
- [x] 기존 코드를 Concurrent 패키지로 변경
- [x] OOP 만족하면서 기능 요구사항 구현
  - [x] HTTP request 데이터를 위한 클래스 만들기
  - [x] http://localhost:8080/index.html 로 접속했을 때 src/main/resources/static 디렉토리의 index.html 파일을 읽어 클라이언트에 응답
  - [x] 서버로 들어오는 HTTP Request의 내용을 읽고 적절하게 파싱해서 로거(log.debug)를 이용해 출력
      - [x] '적절하게'를 정의

## 👨‍💻 미션 중 나의 고민
- 1️⃣ : SRP 원칙
  - RequestHandler에서 request에 대한 response 분기처리 --> RequestMapper
  - RequestMapper 내부에서 http mehthod 별로 MethodRequestHandler의 구현체로 분리
  - 각 구현체에서 path 별로 response 생성
  - 
- 2️⃣ : Concurrent 패키지 사용
  - Runnable 직접 생성 후 주입 방식 대신 newCachedThreadPool 클래스 사용
  - 
- 3️⃣ : primitive 타입 지양
  - Enum, Wrapper 클래스 사용

## ❓ 의문점 및 트러블 슈팅

- [x] 하나의 local에서 서버 실행하고, 브라우저로 클라이언트 연결 시도를 하면 thread가 2개 생성된다는 log가 찍힌다. 하나의 요청에 왜 두 번 tcp 연결이 될까?
    - A : 브라우저에서 개발자 페이지 열면 내가 요청보낸 것 뿐만 아니라 favicon.ico로 요청이 자동으로 하나 더 간다. 현재는 이 요청에 대한 response도 hello world 이다.
- [x] RequestHandler에서는 connection을 close 시킨적 없다. 그럼 처음 요청이 return 되면 thread가 종료되는데, 해당 tcp 연결은 유지되는가? --> 자원 누수?
    - A : 자원 누수가 맞다. client가 처음 요청을 보내면 RequestHandler가 return하고 tcp(socket)을 close 시키지 않는다. 이때 client는 연결이 유지되어 있으니 재요청을 보내면 기존 tcp 연결로 요청을 보내게 된다. </br>
      근데 서버에서는 실행가능한 thread가 없으니 응답을 못하고 client는 응답을 못받으니 timeout 나서 새로운 tcp 연결을 시도하게 된다. log를 보면 재요청마다 port 번호가 바뀐다.

## 👂 다른 사람은 어떻게 구현? (그룹세션)

| 이름  | 구현 내용                                                                                                                       |
|-----|-----------------------------------------------------------------------------------------------------------------------------|
| 최재현 | 1. 서버 포트 결정 로직 메소드 분리 </br> 2. try-with-resource 구문으로 socket, stream close </br> 3. http method 에 따라 메소드 분리 --> oop 리팩토링 계획 |
| 이성훈 | 1. 기본 default static 파일 경로를 지정해두고 client의 요청의 header를 parsing 해서 추가 경로 resource들 한 번에 반환                                    |
| 김명성 | 1. 성훈님과 동일하게 진행 2. newCachedThreadPool은 스레드 개수 제한을 두지 않으면 OOM이 발생할 수 있음.                                                    |

## 📚 추가 학습 개념
- http 프로토콜 header의 keep-alive 속성 (tcp 연결 유지)
- Java에서 Thread 변천사
- Java의 Concurrent 패키지
- 브라우저가 추가 Resource를 요청하는 흐름

## 🧐 회고
- spring이 제공하는 추상화된 기능만 쓰다가 직접 raw하게 구현해볼 수 있었다. 
- 멀티 스레드 환경을 구현하면서 java의 concurrent 패키지를 학습해볼 수 있었다. 
- 그룹 세션을 하면서 각자 생각하는 중요한 관점에 따라 다르게 구현되는 것을 몸소 느꼈다. 
  - 성훈님과 명규님은 로직이 해결하고자 하는 문제에 집중해서 메소드를 우선 분리하고, 클래스 분리로 리팩토링 하셨다. 
  - 재현님은 요청의 method에 따라서 로직을 분리 시키려고 하셨다.
  - 나는 http 메서드 기반으로 클래스가 나뉘고, 그 안에서 서버가 serve하는 자원에 따라 메소드를 분리시키려고 했다.
- 코드를 작성하는 데에서도 각자만의 개성이 나왔다. 
  - 성훈님은 클래스 기반의 SRP를 중요하게 생각하시는 것 같았다. 
  - 재현님은 깔끔한 코드를 지향하신다. 
  - 명규님은 자원 관리에 중점을 두신 것 같다. thread Pool의 차이를 공부하셨다.
- 각자의 코드를 공유하며 장단점을 느꼈고, 개발에는 정답이 없다는 것을 다시 한 번 상기시켰다. 
