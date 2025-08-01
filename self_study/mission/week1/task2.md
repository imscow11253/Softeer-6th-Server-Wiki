# 웹 서버 2단계 - 다양한 컨텐츠 타입 지원

작성자 - 권민혁

## ✅ 할 일
- [x] MIME type 학습
- [x] 브라우저가 html 파일을 로드할 때 resource fetch 방식 학습
- [x] 다양한 확장자 지원 기능 구현 
  - [x] css 
  - [x] js
  - [x] ico
  - [x] png
  - [x] jpg
  - [x] svg

## 👨‍💻 미션 중 나의 고민
- 1️⃣ : 새로운 확장자를 가진 정적 파일이 추가되어도 변경이 쉬운 구조
  - contentType을 enum 클래스로 두어서 request path의 확장자와 mapping 하고, request header의 accept에 명시된 mime 타입과도 맞는지 확인하는 방식을 사용했다.
  - 새로운 Mime type을 추가해야 한다면 ContentType.class 파일만 수정하면 된다. 

## ❓ 의문점 및 트러블 슈팅

- [x] HTTP response의 header에 content-type을 명시했으나 svg 파일을 로드하지 못홤
  - A : response header에 content-length를 넣지 않아서 생긴 문제였다. body의 byte 단위 크기를 명시해주니 해결되었다. 

## 👂 다른 사람은 어떻게 구현? (그룹세션)

| 이름  | 구현 내용                                  |
|-----|----------------------------------------|
| 최재현 | header의 타입을 naive하게 mapping 하는 방식으로 구현 |
| 이성훈 | header의 타입을 naive하게 mapping 하는 방식으로 구현 |
| 김명규 | URLconnection의 메소드 구현 사용               |

## 📚 추가 학습 개념
- MIME type 종류 학습
- 브라우저의 자원 요청 방식 학습

## 🧐 회고
- 예전에 사진 파일과 json 파일을 하나의 요청에서 같이 받기 위해서 multipart 데이터 형식으로 받았던 적이 있다. 당시에는 그냥 아무생각 없이 사용만 했었는데, 
이제는 mime 타입임을 이해했고, 다양한 mime 타입도 확인할 수 있었다. 
- http 프로토콜을 통해 다양한 확장자의 파일들을 주고받는 기능을 짤 수 있게 되었다. 
- 새로운 확장자가 추가되면 서버가 response의 header에 content-type을 명시하는 로직을 추가해야 한다. 과제에서만 주어진 형식이 아닌 다른 확장자를 추가하게 되더라도 변경이 최소화 되는 구조를 고민했다. 
  - content-type을 결정하는 것을 enum 클래스로 두어서 결정하게 했다. 추가가 되면 해당 enum 클래스만 수정하면 된다. 
  - 근데 아직 enum 클래스를 수정해야 한다는 점에서 완벽한 OCP를 만족하지는 못한 것 같다. 
  - 다른 사람 코드를 내일 그룹 회고에서 확인해 보고 싶다. 