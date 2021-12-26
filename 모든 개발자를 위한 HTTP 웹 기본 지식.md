# 모든 개발자를 위한 HTTP 웹 기본 지식
## HTTP 상태코드
### 4xx
> Client Error
- **400 Bad Request**
    - 클라이언트가 요청 내용을 다시 검토하고 보내야함
    - 요청 파라미터가 잘못되거나 API 스펙에 맞지 않게 요청을 할 때
<br><br>
- **401 Unauthorized**
    - 클라이언트가 요청 리소스에 대해 인증이 필요함
    - 로그인이 필요함
<br><br>
- **403 Forbidden**
    - 로그인은 했지만 요청 리소스에 대한 권한이 없음
<br><br>
- **404 Not Found**
    - 요청 리소스가 서버에 없음
### 5xx
> Server Error
- **500 Internal Server Error**
    - 서버 내부 문제
<br><br>
- **503 Service Unavailable**
    - 서비스 이용 불가
    - 서버가 예정된 작업으로 잠시 요청을 처리할 수 없음
## HTTP 헤더1 - 일반 헤더
### 표현
|종류|설명|
|---|---|
|Content-type|표현 데이터의 형식|
|Content-Encoding|표현 데이터의 압축 방식|
|Content-Language|표현 데이터의 자연 언어|
|Content-Length|표현 데이터의 길이|
### Content-Type
> 표현 데이터의 형식 설명
- 미디어 타입, 문자 인코딩
  - text/html; charset=utf-8
  - application/json
  - image/png
### Content-Encoding
> 표현 데이터 인코딩
- 표현 데이터를 압축하기 위해 사용
- 데이터를 전달하는 곳에서 압축 후 인코딩 헤더 추가
- 데이터를 읽는 쪽에서 인코딩 헤더의 정보로 압축 해제
  - gzip
  - deflate
  - identity
### Content-Language
> 표현 데이터의 자연 언어
- 표현 데이터의 자연 언어를 표현
  - ko
  - en
  - en-US
### Content-Length
> 표현 데이터의 길이
- 바이트 단위
- Transfer-Encoding(전송코딩)을 사용하면 Content-Length를 사용하면 안됨
### 협상
> 협상 헤더는 `요청`에만 사용

|종류|설명|
|---|---|
|Accept|클라이언트가 선호하는 미디어 타입 전달|
|Accept-Charset|클라이언트가 선호하는 문자 인코딩|
|Accept-Encoding|클라이언트가 선호하는 압축 인코딩|
|Accept-Language|클라이언트가 선호하는 자연 언어|
### 협상과 우선순위 1
- 0~1, **클수록 높은 우선순위**
- 생략하면 1
- Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7
  1. ko-KR;q=1(생략)
  2. ko;q=0.9
  3. en-US;q=0.8
  4. en;q=0.7
### 협상과 우선순위 2
- 구체적인 것이 우선
- Accept: text/*, text/plain, text/plain;format=flowed, \*/\*
  1. text/plain;format=flowed
  2. text/plain
  3. text/*
  4. \*/\*
### 인증
### Authorization
> 클라이언트 인증 정보를 서버에 전달
- Authorization: Basic xxxxxxxxxxxxxxxxxxxxxx
### 쿠키
- `Set-Cookie`: 서버에서 클라이언트로 쿠키 전달(응답)
- `Cookie`: 클라이언트가 서버에서 받은 쿠키를 저장하고, HTTP 요청시 서버로 전달
- 사용처
  - 사용자 로그인 세션 관리
  - 광고 정보 트래킹
- 쿠키 정보는 항상 서버에 전송됨
  - 네트워크 트래픽 추가 유발
  - 최소한의 정보만 사용(세션 id, 인증 토큰)
### 쿠키 - 생명주기
- Set-Cookie: **expires**=Sat, 26-Dec-2020 04:39:21 GMT
  - 만료일이 되면 쿠키 삭제
- Set-Cookie: **max-age**=3600(3600초)
  - 0이나 음수를 지정하면 쿠키 삭제
### 쿠키 - 도메인
- 명시 : 명시한 문서 기준 도메인 + 서브 도메인 포함
  - domain=example.org를 지정해서 쿠키 생성
  - example.org 가능
  - dev.example.org 가능
- 생략 : 현재 문서 기준 도메인만 적용
  - example.org에서 domain을 생략해서 쿠키 생성
  - example.org 가능
  - dev.example.org 불가능
### 쿠키 - 경로
- 이 경로를 포함한 하위 경로 페이지만 쿠키 접근 가능
- 일반적으로 `path=/` 루트로 지정