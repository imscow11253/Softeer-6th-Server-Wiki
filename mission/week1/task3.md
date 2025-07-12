# 웹 서버 3단계 - GET으로 회원가입

작성자 - 권민혁

## ✅ 할 일
- [x] HTTP GET 메서드 공부
- [x] HTTP GET 파라미터 parsing 학습
- [x] 메인 페이지에서 '회원가입' 버튼 클릭 시, 회원가입 폼 표시
- [x] '로그인' 클릭 시, 유저 map에 저장
  - [x] '로그인' 클릭 시 query parameter와 함께 get 요청이 전달되도록 html 파일 수정
  - [x] url에서 query string 파싱 
  - [x] 유저 map에 멀티 스레드 접근 시 race condition 해결
- [ ] Junit과 AssertJ 학습하기
- [ ] Junit과 AssertJ를 사용해서 테스트 코드 작성하기

## 👨‍💻 미션 중 나의 고민
- 1️⃣ : 2주차 미션 피드백에서 ContentType enum 클래스를 사용하는 방식에 대해서 다른 사람 구현을 참고해보라는 피드백을 받았다. OCP 원칙 위반이어서 나도 고민이 되는 부분이었다. 
  - A : 신지수님의 pr과 명규님의 코멘트를 바탕으로 URLConnection 클래스의 guessContentTypeFromName 메소드를 사용했다. 
- 2️⃣ : 기존 2주차 미션에서는 정적 파일들을 제공하는 API 였다면 이번엔 회원가입 로직을 작성해야 했다. 두 로직 모두 GetRequestHandler 안에 있어서 중복 코드가 좀 발생했다.  
  - A : 공통 로직을 함수로 분리, HTTPRequest, HTTPResponse 객체 생성은 팩토리 클래스 사용하려고 했다.

## ❓ 의문점 및 트러블 슈팅
- [x] 로그인 button 클릭 시 쿼리 파라미터에 input 입력들이 담기지 않음
  - A : Input 태그에 name 속성을 줘야 한다. 해당 name의 값을 Key, 입력된 값을 value로 get 요청의 query parameter가 생성된다. 

## 👂 다른 사람은 어떻게 구현? (그룹세션)

| 이름  | 구현 내용 |
|-----|-------|
| 최재현 |       |
| 이성훈 |       |
| 김명규 |       |

## 📚 추가 학습 개념
- HTTP GET 메소드
- URLConnection.guessContentTypeFromName 메서드

## 🧐 회고
