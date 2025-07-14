# # 📚 HTTP 프로토콜

주간 피드백에서 HTTP 프로토콜의 형식을 제대로 알지 못한 채 웹 서버를 개발해서, http 프로토콜이 제공하는 유연성을 제대로 만족하지 않는 (정확히 말하면 Http 프로토콜을 지원하지 않는) 
웹 서버를 개발했다. 그동안 난 HTTP/1.1 프로토콜의 공식 RFC 문서를 제대로 뜯어본 적이 없다. 그래서 RFC 9112 문서를 기반으로 HTTP/1.1에 대한 깊이 있는 학습을 해보려고 한다.

## HTTP 프로토콜 변천사 
http 버전의 발전 과정을 단순하게 정리하면 다음과 같이 나타낼 수 있다. 

### 1. HTTP/0.9
HTTP 프로토콜이 정식으로 규격화 되기 전의 버전을 명명하기 위한 버전이다. 
```text
//request 
GET /index.html

//response
<HTML>
    hello world
</HTML>
```

### 2. HTTP/1.0
처음으로 HTTP 프로토콜이 규격으로 정해지고 문서화가 되면서 HTTP/1.0이라는 이름이 붙었다. </br>
우리가 익숙한 형태가 이때 처음 등장했다. 
```text
//request 
GET /index.html HTTP/1.0
User-Agent: Mozilla/5.0

//response
200 OK
Date: Sun, 13 Jul 2025 08:21:28 GMT
Content-Type: text/html

<HTML>
    hello world
</HTML>
```

요청/응답 구조가 생겼다. 
- request
  - Request-Line
  - General-Header / Request-Header / Entity-Header 
  - CRLF
  - Entity-Body
- response
  - Status-Line
  - General-Header / Response-Header / Entity-Header
  - CRLF
  - Entity-Body

RFC1945 : https://datatracker.ietf.org/doc/html/rfc1945

### 3. HTTP/1.1
1.0에서 더 많은 기능을 제공하고 공식적으로 표준화시킨 것이 HTTP/1.1이다. 사실상 이때부터 http 프로토콜이 공식적으로 표준화되었다고 본다. 

HTTP/1.0과의 차이점은 다음과 같다. 
1. persist connection
   - 이전에는 요청 한번당 TCP 연결을 한번씩 맺었다. TCP 연결을 위해서는 3-way handshake 과정이 필요한데, 너무 불필요하다. 그래서 http/1.1에서는 tcp 연결을 유지하여서 재요청을 보내면 기존에 맺어둔 tcp 연결을 활용하도록 했다. 
2. pipelinine
   - 이전에는 요청을 보내면 응답이 올 때까지 기다리는 stop & wait 방식을 썼는데, http/1.1에서는 pipelining을 지원해서 응답이 오지 않아도 다음 요청을 미리 보내는 비동기식으로 동작할 수 있도록 한다. 
3. header에 host 필수
   - header에 host를 명시 (어느 도메인 url인지)를 하게 해서 하나의 서버 ip에서 여러 도메인 url 접근을 허용하도록 했다. (= 하나의 ip에서 여러 웹 사이트를 서비스할 수 있도록 했다.)

RFC9112 : https://www.rfc-editor.org/rfc/rfc9112.html

### 4. HTTP/2
Google에서 HTTP 프로토콜의 성능 개선을 목적으로 나온 SPDY 프로토콜을 기반으로 발전된 새로운 HTTP 버전이 HTTP/2이다. 

HTTP/1.1과의 차이점은 다음과 같다.
1. 모든 요청/응답을 병렬처리
   - 이전의 요청에 대한 응답이 지연되면 뒤의 모든 응답이 지연되었는데, 각 요청/응답을 완전 병렬로 처리하여서, 비동기 처리했다.
2. 바이너리 프레이밍
   - 텍스트 단위로 packet을 쪼개는 것이 아니라 바이너리 단위로 쪼개서 보내니 효율적으로 보낼 수 있다. (크기가 작아지고 의미에 구애받지 않는다.)
2. Multiplexed Streams
   - 한 번의 요청만 보내도 여러 개의 응답을 받을 수 있다. 
3. Server Push
   - 요청을 보내지 않아도 서버에서 응답을 보낼 수 있다.  
4. 헤더 압축
   - 바이너리 단위로 보내기 때문에 암호화 알고리즘을 써서 압축할 수 있다. 

RFC9113 : https://datatracker.ietf.org/doc/html/rfc9113

### 5. HTTP/3
HTTP 프로토콜은 TCP 연결 위에서 동작한다. 그래서 TCP에서 생기는 한계를 똑같이 가지고 있는데, 
이를 해결하고자 UDP를 도입한 것이 HTTP/3이다. 

다만 UDP는 신뢰성이 떨어지고, 데이터 순서, 손실을 보장하지 않는다. 
그래서 HTTP3에서는 QUIC이라는 프로토콜을 도입하여서 UDP에서도 신뢰성을 보장할 수 있도록 했다. 

RFC9114 : https://datatracker.ietf.org/doc/html/rfc9114

---

## HTTP/1.1 형식
우리가 웹서버를 구현하면서 HTTP/1.1을 사용한다. 이를 지원하기 위해서는 HTTP/1.1에서 제공하는 format을 알아야지 string parsing도 할 수 있을 것이다. 

다음 문서를 참고해서 정리했다.
https://www.rfc-editor.org/rfc/rfc9112.html

전체적인 구조는 다음과 같다. 
```text
 HTTP-message   = start-line CRLF
                   *( field-line CRLF )
                   CRLF
                   [ message-body ]
```

참고로 CRLF는 줄바꿈 형식으로 \r\n을 의미한다. 이건 기억이 안나면 따로 찾아보자.

### Start-Line (Request-Line)
```text
method SP request-target SP HTTP-version CRLF
```
SP 대신 다른 공백 형식이 들어가도 구분자로 해석할 수 있어야 한다. (HTAB, VT(%x0B), FF(%x0C), bare CR)
근데 공식 RFC에서 유연한 해석을 가능케 허용하는 서버는 Request smuggling 공격이 들어올 수도 있음을 경고한다.

지원되는 HTTP 메소드가 아닌 새로운 메소드가 들어오면 서버는 501(구현되지 않음) error를 반환해야한다.
url이 너무 길게 들어와서 지원되지 않으면 414(URI가 너무 김) error를 반환해야 한다. 
공식 문서에서는 최소 8000 옥텟까지는 지원하는 것이 좋다고 RFC에서 명시한다. 

#### HTTP method
http method는 대소문자를 구분한다. 

#### Request-Target
공백을 허용하지 않는다. 

형식에 따라 4가지 형태가 있다. 
- origin-form : 원본 서버에 직접 요청할 때
```text
absolute-path [ "?" 쿼리]

ex
GET /where?q=now HTTP/1.1
Host: www.example.org
```
- absolute-form : 프록시 서버에 요청할 때
```text
absolute-URI

ex
GET http://www.example.org/pub/WWW/TheProject.html HTTP/1.1
```
- authority-form : CONNECT 메소드를 사용할 때 사용
```text
uri-host ":" port

ex
CONNECT www.example.com:80 HTTP/1.1
Host: www.example.com
```
- asterisk-form : 서버 전체에게 OPTIONS 요청을 보낼 때 사용
```text
"*"

ex
OPTIONS * HTTP/1.1
```

#### HTTP-version
대소문자를 구분한다.

### Start-Line (Status-Line)
```text
HTTP-version SP status-code SP [ reason-phrase ] CRLF
```
여기서도 request-line과 동일하게 SP 대신 다른 형식의 공백을 허용할 수도 있지만 경우에 따라 request smuggling에 취약할 수 있으므로 주의해야 한다. 

#### Status code 
상태코드는 3자리 정수다. 이 값은 미리 정해져 있다.

### Header
```text
필드-이름 ":" OWS 필드-값 OWS CRLF
```
OWS는 optional whitespace의 약자로, 공백이 있어도 되고, 없어도 된다는 뜻이다. 사람이 읽기 쉽도록 일반적으로 OWS로는 SP를 사용한다고 한다. 그리고 HTAB도 가능하다. (http 파서에서는 SP와 HTAB만 OWS로 구분해야 한다.)
필드 이름과 콜론 사이에는 공백을 사용할 수 없다. (만약 허용할 시, 보안 취약점이 될 수 있다. 공백이 있으면 400 error를 반환해야 한다.)
맨 앞, 맨 뒤에도 공백을 허용하지 않는다. 있으면 파서에 의해 무시된다. 

값이 너무 길어도 줄 바꿈은 허용되지 않는다.

요청에서는 Host를 필수적으로 명시해야 한다.
만약 Host 헤더가 없거나 여러 줄이면 400(잘못된 요청)으로 응답해야 한다.

header의 key값은 대소문자를 구분하지 않는다. --> 브라우저는 알아서 해주는데 우리가 구현할 웹 서버에서 하드코딩하면 안된다.

## parsing 시에 주의점

1. 유니코드 형식으로 읽으면 보안 취약점이 발생할 수 있다. 무조건 ASCII 코드로 읽어야 한다. 
유니코드로 해석할 시, 보안 취약점이 생기고 http 메시지가 잘못 해석될 수 있다.https://www.rfc-editor.org/rfc/rfc9112.html#name-message-parsing


2. start line과 header의 각 필드 구분을 CRLF를 사용해서 줄바꿈하는 것이 원칙이지만 LF만 해도 줄 종료문자로 인식할 수 있다. CR만 사용하는 것은 안된다. (CR만 쓰고 싶으면 SP로 대체해서 쓴다.)


3. 요청의 시작,끝에 CRLF를 추가해선 안된다. body 마지막에 CRLF를 넣고 싶으면, content-length에 해당 CRLF 길이도 포함해야 한다. (400 error)


4. 시작줄과 header 사이에 공백줄을 넣으면 안된다. (400 error)








