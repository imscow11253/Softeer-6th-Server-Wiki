# 웹 서버 1단계 - index.html 응답

작성자 - 권민혁

## ✅ 할 일
- [x] 기존 코드 분석
- [x] 자바 버전별 Thread 모델 학습
- [x] 자바 Concurrent 패키지 학습
- [x] 기존 코드를 Concurrent 패키지로 변경
- [ ] OOP 만족하면서 기능 요구사항 구현
  - [x] HTTP request 데이터를 위한 클래스 만들기
  - [ ] http://localhost:8080/index.html 로 접속했을 때 src/main/resources/static 디렉토리의 index.html 파일을 읽어 클라이언트에 응답
  - [ ] 서버로 들어오는 HTTP Request의 내용을 읽고 적절하게 파싱해서 로거(log.debug)를 이용해 출력
      - [ ] '적절하게'를 정의

## 👨‍💻 미션 중 나의 고민
- 1️⃣ : SRP 원칙
  - WebServer.defineServerPort (서버 포트 결정)
  - WebServer.waitingClient (서버가 클라이언트 요청 대기)
- 2️⃣ : Concurrent 패키지 사용
  - Runnable 직접 생성 후 주입 방식 대신 newCachedThreadPool 클래스 사용
- 3️⃣ : 
- ...

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

## 🧐 회고
