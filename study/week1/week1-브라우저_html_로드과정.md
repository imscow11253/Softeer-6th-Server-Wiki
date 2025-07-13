# 📚 브라우저가 html 코드를 받아올 때 추가 resource를 fetch 하는 과정

25.7.10

## 흐름

1. 브라우저가 서버에게 /index.html 요청을 보내고 받는다.
2. parser가 라인 단위로 읽으면서 html 태그들을 해석해서 추가 요청이 필요한지 판단한다.
   - link 태그 : css 파일
   - script 태그 : js 파일
   - img 태그 : jpeg, jpg, png, svg 등의 파일
3. 각 태그에서 속성 값 (href, src 등)에 명시된 파일 이름을 그대로 path에 담아 요청을 보낸다. 
```html
<head>
  <link rel="stylesheet" href="main.css">
  <script src="main.js"></script>
</head>
<body>
  <img src="logo.png">
</body>
```
```text
GET /main.css HTTP/1.1
GET /main.js HTTP/1.1
GET /logo.png HTTP/1.1
```

4. 응답 받은 파일에 대한 확장자를 header의 Content-Type으로 확인한다.
5. 파일 렌더링을 해서 화면에 적용한다. 