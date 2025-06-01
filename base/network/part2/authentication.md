# Cookie 무엇일까?
- 웹사이트가 사용자 브라우저에 저장하는 작은 데이터 조각
- Stateless
- 주요 속성
  - HttpOnly
    - JavaScript 코드를 통한 쿠키 접근 방지
  - Secure
    - HTTPS 연결에서만 쿠키 전송
  - SameSite
    - 교차 사이트 요청 시 쿠키 전송 제어
    - Strict => 서브 도메인을 포함한 동일한 사이트
    - Lax => 동일 사이트 + HTTP GET 요청
    - None => 모든 사이트
<br>

## 쿠키 기반 인증은 어떻게 이루어질까?
- 클라이언트는 로그인 요청
- 서버는 응답 헤더에(Set-Cookie) 쿠키 담아 응답
- 클라이언트는 매번 요청마다 헤더에(Cookie) 쿠키 담아 전송
<img width="669" alt="Image" src="https://github.com/user-attachments/assets/a49d8151-2934-4ad6-bde5-af51c16837cf" />
<br></br>

## 쿠키의 종류에는 무엇이 있을까?
- 세션 쿠키
  - 만료일을 지정하지 않아 브라우저 종료 시 삭제됨
- 영속 쿠키
  - 지정한 만료일까지 유지됨
<br>

## 쿠키 사용 시 문제점이 있을까?
- 탈튀 가능
- 용량이 제한적이라 저장할 수 있는 정보가 한정적
<br>

## XSS(Cross-site Scripting) 무엇일까?
- 웹사이트(입력폼)에 악성 JavaScript 코드를 삽입하여 사용자의 브라우저에서 실행되게 하는 방식
- 쿠키 탈취 가능
<br>

## CSRF(Cross-site Request Forgery) 무엇일까?
- 사용자가 의도하지 않은 요청을 악의적인 웹사이트에서 대신 보내는 방식
<br>

## SameSite의 Strict 옵션의 한계가 무엇일까?
- 사용 중인 웹사이트 내에서 다른 사이트로 이동하고 요청하면 다시 인증해야 하는 불편함
- Lax 옵션은 GET 요청도 허용하여 민감한 정보에 접근 가능
  - 대안으로는 CSRF 토큰과 함께 사용
<br>

## CSRF 토큰이 무엇일까?
- 사용자가 웹사이트에서 직접 요청할 때만 CSRF 토큰 포함
- 악의적인 사이트는 토큰을 탈취할 수 없어서 대신 요청 불가능
<br>

# Session 무엇일까?
- 서버와 클라이언트 간의 상태를 유지하기 위한 메커니즘
- Stateful
- 서버에서 사용자 데이터를 관리하고, 클라이언트에 식별할 수 있는 세션 ID 제공
<img width="525" alt="Image" src="https://github.com/user-attachments/assets/1e08ea37-3954-4e1c-bb5c-7881241eb085" />
<br></br>

## 세션 기반 인증은 어떻게 이루어질까?
- 클라이언트는 로그인 요청
- 서버는 세션 ID 통해 인증 관련 데이터를 저장하고, 응답 헤더에(Set-Cookie) 세션 ID 담아 응답
- 클라이언트는 매번 요청마다 헤더에(Cookie) 세션 ID 담아 전송
- 서버는 요청받은 세션 ID 검증
<img width="474" alt="Image" src="https://github.com/user-attachments/assets/af5c21d8-5ffd-4cad-8bb6-ff766f35a498" />
<br></br>

## 세션 방식에 문제점이 있을까?
- 세션 ID 탈취
- 서버에서 인증 관련 데이터를 관리하여 네트워크 트래픽 증가
<br>

# Token 무엇일까?
- 사용자 인증 및 권한 부여를 위한 암호화된 문자열
- Stateless
- 대표적으로 JWT 토큰이 있음
<img width="476" alt="Image" src="https://github.com/user-attachments/assets/db4a96e0-610d-435c-b6d6-5f1ea2f4a1f5" />
<br></br>

## 토큰 방식에 문제점이 있을까?
- 토큰 탈취
- 토큰에 인증 관련 정보가 많아질수록 네트워크 트래픽 증가
<br>

## 토큰 탈취를 어떻게 예방할 수 있을까?
- HTTP 헤더 Set-Cookie에 토큰 담아 전송
  - HttpOnly, Secure, SameSite 옵션 설정
- 비밀번호 변경, 결제 등 중요한 작업에는 추가 인증 도입
<br>

## JWT(Json Web Token) 무엇일까?
- 인증과 관련된 정보를 암호화한 JSON 토큰
- Header.Payload.Signature 구성
- Access/Refresh Token 사용
<img width="716" alt="Image" src="https://github.com/user-attachments/assets/7a3ef9e5-dc2d-4760-9c79-a363b3fe2d3c" />
<br></br>

### Header 어떻게 구성될까?
- 해시 알고리즘
- 토큰 타입
```jsonc
{
  "alg": "HS256",  // 사용할 해시 알고리즘
  "typ": "JWT"     // 토큰의 타입
}
```
<br>

### Payload 어떻게 구성될까?
- Claim 집합
  - Key-Value 구조로 인증 관련 정보 포함
  - 클라이언트와 서버가 실질적으로 사용하는 데이터
```jsonc
{
  "sub": "1234567890",        // subject claim (등록된 claim)
  "name": "홍길동",             // 공개 claim
  "iat": 1516239022,          // issued at claim (등록된 claim)
  "exp": 1516242622,          // expiration time claim (등록된 claim)
  "role": "manager"           // 비공개 클레임: 사용자 역할
}
```
<br>

### Claim에는 어떤 종류가 있을까?
- 등록된 클레임
  - JWT 표준 클레임
  - iss (Issuer, 발급자)
  - sub (Subject, 사용자 식별자)
  - exp (Expiration, 만료 시간)
  - iat (Issued At, 발급 시간)
- 공개 클레임
  - 여러 서비스에서 공통으로 이해할 수 있는 표준 클레임
  - 동일한 의미로 쉽게 이해할 수 있음
- 비공개 클레임
  - 서비스 제공자와 사용자 간에 합의된 사용자 정의 클레임
  - 해당 서비스 내에서만 알 수 있음
```jsonc
{
  "iss": "auth.myapp.com",        // 등록된 클레임: 발급자
  "sub": "user_12345",            // 등록된 클레임: 사용자 ID
  "iat": 1622419200,              // 등록된 클레임: 발급 시간
  "exp": 1622505600               // 등록된 클레임: 만료 시간
}
```
```jsonc
{
  "name": "홍길동",                 // 공개 클레임: 표준화된 이름
  "email": "hong@example.com"     // 공개 클레임: 표준화된 이메일
}
```
```jsonc
{
  "role": "manager",              // 비공개 클레임: 사용자 역할
  "department": "marketing",      // 비공개 클레임: 부서
  "access_level": 3               // 비공개 클레임: 접근 권한 레벨
}
```
<br>

### Signature(서명) 어떻게 구성될까?
- Header, Payload 각각 Base64Url 인코딩
- 생성된 문자열과 비밀키를 통해 HMAC(Hash-based Message Authentication Code) 알고리즘으로 해싱
- 해시값을 Base64Url 인코딩
```javascript
HMACSHA512(
    base64UrlEncode(header) + "." + base64UrlEncode(payload),
    secret_key
)
```
=> 서버는 클라이언트로부터 받은 토큰의 Header, Payload 그리고 비밀키를 통해 서명과 비교하여 토큰 검증
<br></br>

### Base64 인코딩은 어떤 과정으로 이루어질까?
- 문자열을 8비트 기준 바이너리 데이터로 변환
- 6비트 단위로 쪼개서 새로운 문자열 생성 (알파벳 대소문자, 숫자, +, / 등 => 64개)
- 디코딩은 반대로 적용
<br>

### Base64와 Base64Url 인코딩 방식에는 어떤 차이가 있을까?
- 클라이언트와 서버는 토큰을 Url 파라미터로 주고받을 수 있음
- Base64의 +, /는 각각 공백과 경로 구분자를 나타내서 문제가 발생할 수 있음
- Base64Url은 안전한 인코딩을 위해 -, _ 사용
<br>

### Access/Refresh Token 용도는 무엇일까?
- Access Token
  - API 리소스 접근 권한 부여
  - 인증된 사용자 식별
  - 짧은 수명을 가짐
- Refresh Token
  - 새로운 Access Token 발급
  - 사용자가 반복적으로 인증하지 않도록 함
  - 긴 수명을 가짐
<br>

### Access Token 수명이 짧은 이유가 무엇일까?
- 토큰이 탈취되는 경우 피해 최소화
- 토큰 내용 갱신으로 최신 정보 반영
<br>

### Authentication Scheme 무엇일까?
- HTTP 인증 헤더는 Scheme와 Credentials 함께 명시하는 것이 표준
- 보통은 소지자 기반 인증인 Bearer 타입 사용
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
                      .eyJzdWIiOiIxMjM0NTY3ODkwIn0
                      .dozjgNryP4J3jVmNHl0w5N_XgL0n3I9PlFUP0THsR8U
```
<br>

## OAuth 2.0 무엇일까?
- 제 3자 애플리케이션이 사용자 데이터에 접근 가능
  - 사용자는 권한 부여 범위(접근 가능한 데이터)를 선택적으로 허용
  - 권한 취소도 가능
- Access/Refresh Token 사용
  - Access Token
    - JWT 또는 다른 구조의 토큰
  - Refresh Token
    - 클라이언트는 내용을 읽을 수 없는 불투명 토큰
<br>

### 불투명 토큰을 어떻게 생성할까?
- 암호학적으로 안전한 임의의 문자열 생성
- 토큰과 서버 비밀키를 해싱
<br>

### OAuth 2.0 기반 인증은 어떻게 이루어질까?
- 클라이언트는 여러 파라미터와 함께 사용자 인증 및 권한 부여
- 인증 서버는 인증 코드 발급
- 클라이언트는 발급받은 인증 코드와 함께 토큰 요청
- 인증 서버는 토큰 발급
- 클라이언트는 토큰을 통해 사용자 리소스에 접근
```
-- 클라이언트 -> 인증 서버
-- 인증 요청
https://auth-server.com/oauth/authorize?
response_type=code                             -- 인증 코드 요청
&client_id=CLIENT_ID                           -- 클라이언트 식별자
&redirect_uri=https://client-app.com/callback  -- 인증 후 사용자가 리디렉션 될 URI
&scope=profile email                           -- 요청하는 접근 범위
&state=random_string_for_csrf_protection       -- CSRF 공격 방지를 위한 문자열
```
```
-- 사용자 리디렉션
https://client-app.com/callback?
code=AUTHORIZATION_CODE
&state=random_string_for_csrf_protection
```
<br>

### OAuth 1.0과 2.0 어떤 차이가 있을까?
- OAuth 1.0
  - 세션 고정 공격에 취약
  - 복잡한 구현
- OAuth 2.0
  - 재설계되어 이전 버전과 호환되지 않음
<br>

# Reference
[https://inpa.tistory.com/entry/WEB-📚-JWTjson-web-token-란-💯-정리](https://inpa.tistory.com/entry/WEB-📚-JWTjson-web-token-란-💯-정리)
<br>
[https://dev-coco.tistory.com/61](https://dev-coco.tistory.com/61)
<br>
