# 📚Multipart-Form data 정리 

이미지와 글을 하나의 post 요청으로 받으려고 한다. 그러면 http content-type이 mutpart/form-data 형식이어야 한다. 
그래서 이 content tpye에 맞는 body parser를 만들어야 하는데 1주차 피드백에서 http RFC 문서를 보라는 피드백을 
받았기 때문에 Body Parser를 구현하기 전에 Multipart/form-data에 대한 RFC 문서인 RFC 7578을 읽고 정리하려고 한다. (2388은 옛날 버전이다.) 

https://datatracker.ietf.org/doc/html/rfc7578

## 1. 정의

여러개의 MIME data를 한 번에 표현하는 형식이다. 
아래 링크에 정의된 multipart MIME data 의 정의를 그대로 따른다. boundary에 따라서 각 multipart MIME data가 구분된다.

https://datatracker.ietf.org/doc/html/rfc2046#section-5.1

### boundary 
CRLF, "--", "boundary" 파라미터로 구성된다. boundary 파라미터는 header의 content-type에서 랜덤 문자열로 다음처럼 주어진다. 

보통은 라이브러리나 브라우저가 자동으로 생성해준다. 근데 직접 생성해야 할 때는 본문의 내용과 겹치지 않도록 unique한 값을 설정해야 한다. 

각 part의 시작은 `--[boundary]` 이렇게 시작하고 body가 끝나면 `--[boundary]--` 로 끝낸다.

```text
Content-Type: multipart/form-data; boundary=----WebKitFormBoundarybtO3GPNA8vu5pv3B
```

### Content-Disposition header field
각 파트마다 content-disposition이라는 헤더 필드가 있고, type은 항상 "form-data"이다. 그리고 항상 "name"이라는 파라미터를
필수적으로 가지고 있어야 한다. 
```text
Content-Disposition: form-data; name="user"
```

파일이 들어올 경우, filename 이라는 파리미터로 파일 이름을 표시해줘야 한다. 필수는 아니라고 한다. 
```text
Content-Disposition: form-data; name="image"; filename="metamong.png"
```

### Content-Type header field
명시되어 있지 않았을 때 default는 text/plain이라고 한다. 문자열인 경우 charset 파라미터에 인코딩 방식을 지정할 수 있다. 

파일 내용일 경우 파일의 타입을 적거나 파일의 default인 application/octet-stream 으로 보내진다고 한다. 

### 주의점
multipart/form-data는 3가지 header만 허용한다. 
- Content-Type
- Content-Disposition
- Content-Transfer-Encoding (이건 제한적인 상황에서만)

그외의 헤더는 있으면 오류가 난다. 

### 예시 
```text
--AaB03x
content-disposition: form-data; name="field1"
content-type: text/plain;charset=UTF-8

Joe owes =E2=82=AC100.
--AaB03x
```

## 2. 해석 및 format
각 mime 파트의 헤더는 US-ASCII 인코딩 방식으로 해석된다. 파일 이름은 다른 방식으로 인코딩을 하는 경우도 있지만 대부분의 경우 UTF-8이라고 한다. 

## 3. Multipart/form-data에서 mime data를 하나만 보내면 어떻게 될까?
직접 해봤다.
- png 이미지와 텍스트를 보냈을 떄
```text
 ------WebKitFormBoundaryJqeB8vZCAlU8KEfS
Content-Disposition: form-data; name="image"; filename="metamong.png"
Content-Type: image/png

...

------WebKitFormBoundarymT5huy6ZkPY9P76N
Content-Disposition: form-data; name="caption"

asdfasdf
------WebKitFormBoundarymT5huy6ZkPY9P76N--
```

- 텍스트만 보냈을 때  

얘가 좀 특이한데, image 부분에 content-type이 application/octet-stream 으로 오게 된다. 내용은 비워진 상태로 온다. 
```text
 ------WebKitFormBoundarymT5huy6ZkPY9P76N
Content-Disposition: form-data; name="image"; filename=""
Content-Type: application/octet-stream


------WebKitFormBoundarymT5huy6ZkPY9P76N
Content-Disposition: form-data; name="caption"

asdfasdf
------WebKitFormBoundarymT5huy6ZkPY9P76N--
```

- 이미지만 보냈을 때
```text
------WebKitFormBoundaryGbjRHN011MN0wUZV
Content-Disposition: form-data; name="image"; filename="metamong.png"
Content-Type: image/png


------WebKitFormBoundaryGbjRHN011MN0wUZV
Content-Disposition: form-data; name="caption"


------WebKitFormBoundaryGbjRHN011MN0wUZV--
```







