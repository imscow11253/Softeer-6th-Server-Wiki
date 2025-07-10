# 📚 서버에서 제공하는 MIME type 정리 

25.7.10

## MIME 타입이란?

Multipurpose Internet Mail Extensions의 약자로, 문서, 파일 또는 바이트 집합의 성격과 형식을 나타낸다.
IETF의 RFC 6838에 표준화되어 있다. 

종류가 많다.
https://developer.mozilla.org/ko/docs/Web/HTTP/Guides/MIME_types/Common_types

기본적인 형태는 다음과 같다.
type은 image, text 같은 읿반적인 카테고리를 의미하고,
sybtype은 세부적으로 html,css,jpeg 등이 있다. 
```text
type/subtype
```

다음과 같이 매개변수를 추가할 수도 있다. 
```text
type/subtype;parameter=value

ex)
text/html; charset=UTF-8
```

타입이 한개인가 여러개인가에 따라서도 유형이 나뉜다. 
- 개별 타입
- multipart 타입

내가 이미지랑 json을 같이 받을 때 항상 사용했던 multipartformdata가 이런 개념에서 나온 것이었다. </br>
http의 content-type을 잘 알아두면 다양한 확장자의 파일들을 http 프로토콜로 보낼 수 있다는 점이 좋은 것 같다. 

레퍼런스
https://developer.mozilla.org/ko/docs/Web/HTTP/Guides/MIME_types
