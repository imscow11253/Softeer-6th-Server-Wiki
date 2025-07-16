# 📚 HTTP Response의 Status code 정리

- 1xx : informational response
    - 요청을 서버가 수신했고 처리가 진행 중임을 알리는 목적으로 사용된다.
- 2xx : successful
    - 요청이 성공적으로 수신, 이해, 처리되었음을 의미한다.
- 3xx : redirection
    - 요청을 완료하기 위해 추가적인 동작이 필요함을 나타낸다.
- 4xx : client error
    - 잘못된 요청 등 클라이언트 측의 오류를 의미한다.
- 5xx : server error
    - 서버에서 오류가 발생하여 요청을 처리하지 못한 경우

## 1xx : informational response
1. 100 : Continue
2. 101 : Switching Protocols
3. 102 : Processing
4. 103 : Early Hints

## 2xx : success
1. 200 : OK
2. 201 : Created
3. 202 : Accepted
4. 203 : Non-Authoritative Information
5. 204 : No Content
6. 205 : Reset Content
7. 206 : Partial Content
8. 207 : Multi-Status
9. 208 : Already Reported
10. 226 : IM Used

## 3xx : redirection
1. 300 : Multiple Choices
2. 301 : Moved Permanently
3. 302 : Found
4. 303 : See Other
5. 304 : Not Modified
6. 305 : Use Proxy
7. 306 : Switch Proxy (사용되지 않음)
8. 307 : Temporary Redirect
9. 308 : Permanent Redirect

## 4xx : client error
1. 400 : Bad Request
2. 401 : Unauthorized
3. 402 : Payment Required
4. 403 : Forbidden
5. 404 : Not Found
6. 405 : Method Not Allowed
7. 406 : Not Acceptable
8. 407 : Proxy Authentication Required
9. 408 : Request Timeout
10. 409 : Conflict
11. 410 : Gone
12. 411 : Length Required
13. 412 : Precondition Failed
14. 413 : Payload Too Large
15. 414 : URI Too Long
16. 415 : Unsupported Media Type
17. 416 : Range Not Satisfiable
18. 417 : Expectation Failed
19. 418 : I'm a teapot (Easter egg)
20. 421 : Misdirected Request
21. 422 : Unprocessable Entity
22. 423 : Locked
23. 424 : Failed Dependency
24. 425 : Too Early
25. 426 : Upgrade Required
26. 428 : Precondition Required
27. 429 : Too Many Requests
28. 431 : Request Header Fields Too Large
29. 451 : Unavailable For Legal Reasons

## 5xx : server error
1. 500 : Internal Server Error
2. 501 : Not Implemented
3. 502 : Bad Gateway
4. 503 : Service Unavailable
5. 504 : Gateway Timeout
6. 505 : HTTP Version Not Supported
7. 506 : Variant Also Negotiates
8. 507 : Insufficient Storage
9. 508 : Loop Detected
10. 510 : Not Extended
11. 511 : Network Authentication Required