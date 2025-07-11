# 📚 HTTP GET 메서드

25.7.11

## GET 메서드 
특정 리소스를 가져올 때 (CRUD 중 read) 사용하는 메서드이다. </br>
body 값이 없는 것이 정석이다. </br>

body가 없다면 특정 정보를 어떻게 전달할 수 있을까? </br>

## Query parameter
HTTP 프로토콜은 url 뒤쪽에 ? 카워드 이후로 {key}:{value} 형태로 파라미터를 넣을 수 있다. </br>
& 키워드를 구분자로 여러 파라미터를 넣을 수 있다. 

쿼리 파라미터로 한글을 적고 싶을 수 있다. 
```text
ex) https://naver.com/user?name=권민혁
```
근데 http url에 한글을 넣고 싶으면 URL 인코딩을 사용해야 한다. 
ASCII 문자가 아닌 문자를 포함하면 브라우저나 서버가 올바르게 해석하지 못할 수 있다. 

```java
      String encodedValue = request.getParameter("param");
      String decodedValue = java.net.URLDecoder.decode(encodedValue, "UTF-8");
```

## Restful API
Restful 하다는 url은 자원을 path 두고, method로 행위를 표현하는 것이라 이해했다. 

GET 메서드는 CRUD 중 READ를 위한 http method이다. path에 자원을 표시하고, GET 요청을 쓰면 해당 자원을 읽어오고 싶다는 뜻이다. </br>
이처럼 Restful하게 API를 작성하면 url 만으로도 어떤 작업이 이루어질지 예측할 수 있다. 

## query parameter 추출하기
Spring에서는 어노테이션을 사용하면 url의 query parameter를 쉽게 추출할 수 있지만, </br>
우리는 naive한 java로 웹서버를 만들고 있기 때문에 query parameter를 받아오는 방식이 다르다. 

직접 url path에서 parsing 하는 방식을 사용해야 한다. 
```java
String path = "naver.com/user?name=kwon&password=min";
String[] parameters = path.split("\\?")[1].split("&");

Map<String : String> queryParameter = new HashMap<>();
for(String kvs : parameters){
        String[] kv = kvs.split("=");
        queryPrarameter.put(kv[0], kv[1]);
}
```
