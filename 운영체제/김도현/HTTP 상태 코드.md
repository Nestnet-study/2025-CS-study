

## 1xx - 정보 응답

### 100 Continue

**의미**: 지금까지 상태가 정상이며 클라이언트가 계속 요청해도 됨  
**실제 사용**: 대용량 파일 업로드 시 서버가 헤더를 검증한 후 본문 전송을 허가할 때

### 101 Switching Protocol

**의미**: 서버가 프로토콜을 변경할 것임을 알림  
**실제 사용**: WebSocket 연결 시 HTTP에서 WebSocket으로 프로토콜 업그레이드

---
## 2xx - 성공 응답

### 200 OK

**의미**: 요청이 성공적으로 처리됨  
**주의점**: GET, POST, PUT 등 메서드에 따라 성공의 의미가 다름

- GET: 리소스 조회 성공
- POST: 데이터 처리 성공
- PUT: 리소스 수정 성공

### 201 Created

**의미**: 요청이 성공하여 새로운 리소스가 생성됨  
**실제 사용**: POST로 새 사용자 계정 생성, 게시글 작성 등  
**Location 헤더**: 생성된 리소스의 URL을 포함해야 함

**상황 예시**:

```javascript
// 새 게시글 작성 API
POST /api/posts
{ "title": "안녕하세요", "content": "첫 게시글입니다" }

// 응답
HTTP/1.1 201 Created
Location: /api/posts/123
{ "id": 123, "title": "안녕하세요", "created_at": "2025-01-15" }
```

### 204 No Content

**의미**: 요청 성공했지만 반환할 내용이 없음  
**실제 사용**:

- DELETE 요청 성공 시
- PUT으로 수정 완료 시 (수정된 데이터를 다시 보낼 필요 없을 때)
- 좋아요/북마크 토글 API

**상황 예시**:

```javascript
// 게시글 삭제 API
DELETE /api/posts/123

// 응답 (본문 없음)
HTTP/1.1 204 No Content
```

---

## 3xx - 리다이렉션

### 301 Moved Permanently

**의미**: 리소스가 영구적으로 새 위치로 이동  
**SEO 영향**: 검색엔진이 새 URL로 인덱스 업데이트  
**실제 사용**: 도메인 변경, URL 구조 변경

**상황 예시**:

```javascript
// 구 도메인에서 신 도메인으로 이동
GET http://old-site.com/products

// 응답
HTTP/1.1 301 Moved Permanently
Location: https://new-site.com/products
```

### 302 Found

**의미**: 리소스가 일시적으로 다른 위치에 있음  
**주의점**: 브라우저가 GET으로 메서드를 변경할 수 있음

### 304 Not Modified

**의미**: 캐시된 버전을 사용해도 됨  
**조건**: If-Modified-Since, ETag 등 조건부 요청 헤더 필요  
**성능**: 대역폭 절약과 응답 속도 향상

**상황 예시**:

```javascript
// 조건부 요청 (ETag 포함)
GET /api/users/123
If-None-Match: "abc123def"

// 응답 (리소스가 변경되지 않음)
HTTP/1.1 304 Not Modified
ETag: "abc123def"
```

### 307 Temporary Redirect

**의미**: 302와 같지만 HTTP 메서드 변경 금지  
**차이점**: POST 요청이면 리다이렉트 후에도 POST 유지

---
## 4xx - 클라이언트 에러

### 400 Bad Request

**의미**: 잘못된 요청 문법  
**실제 예시**:
- JSON 형식 오류
- 필수 파라미터 누락
- 잘못된 데이터 타입

**상황 예시**:

```javascript
// 잘못된 JSON 형식
POST /api/users
{ "name": "홍길동", "age": "스물다섯" // 문자열인데 숫자여야 함

// 응답
HTTP/1.1 400 Bad Request
{ "error": "age must be a number" }
```

### 401 Unauthorized

**의미**: 인증이 필요함 (사실은 "Unauthenticated"가 더 정확)  
**WWW-Authenticate 헤더**: 인증 방법을 명시해야 함  
**실제 사용**: 로그인이 필요한 API 접근

**상황 예시**:

```javascript
// 토큰 없이 보호된 API 접근
GET /api/profile

// 응답
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer
{ "error": "Authentication required" }
```

### 403 Forbidden

**의미**: 서버가 클라이언트를 알고 있지만 권한이 없음  
**401과의 차이**:

- 401: "누구세요?" (인증 필요)
- 403: "당신은 알지만 권한 없음" (인가 실패)

**상황 예시**:

```javascript
// 일반 사용자가 관리자 페이지 접근 시도
GET /api/admin/users
Authorization: Bearer valid_user_token

// 응답
HTTP/1.1 403 Forbidden
{ "error": "Admin access required" }
```

### 404 Not Found

**의미**: 요청한 리소스를 찾을 수 없음  
**보안 목적**: 403 대신 404로 리소스 존재 여부를 숨기기도 함  
**API 설계**: 존재하지 않는 사용자 조회 시

**상황 예시**:

```javascript
// 존재하지 않는 게시글 조회
GET /api/posts/99999

// 응답
HTTP/1.1 404 Not Found
{ "error": "Post not found" }
```

### 409 Conflict

**의미**: 요청이 서버 상태와 충돌  
**실제 사용**:

- 이미 존재하는 사용자명으로 가입 시도
- 낙관적 락(Optimistic Lock) 충돌
- 동시 수정 충돌

**상황 예시**:

```javascript
// 이미 존재하는 이메일로 회원가입 시도
POST /api/register
{ "email": "user@example.com", "password": "123456" }

// 응답
HTTP/1.1 409 Conflict
{ "error": "Email already exists" }
```

### 422 Unprocessable Entity

**의미**: 요청 문법은 올바르지만 의미적으로 처리할 수 없음  
**400과의 차이**:

- 400: 문법 오류 (JSON 파싱 실패)
- 422: 의미적 오류 (이메일 형식은 맞지만 유효하지 않은 도메인)

**상황 예시**:

```javascript
// 형식은 맞지만 유효하지 않은 데이터
POST /api/users
{ "email": "user@invalid-domain.xyz", "age": -5 }

// 응답
HTTP/1.1 422 Unprocessable Entity
{ 
  "errors": [
    "Invalid email domain",
    "Age must be positive"
  ]
}
```

### 429 Too Many Requests

**의미**: 요청 빈도 제한 초과 (Rate Limiting)  
**Retry-After 헤더**: 언제 다시 시도할 수 있는지 명시  
**실제 사용**: API 호출 제한, DDoS 방어

**상황 예시**:

```javascript
// 1분에 100회 제한인데 초과 시도
GET /api/search?q=example

// 응답
HTTP/1.1 429 Too Many Requests
Retry-After: 60
{ "error": "Rate limit exceeded. Try again in 60 seconds" }
```

---
## 5xx - 서버 에러

### 500 Internal Server Error

**의미**: 서버에서 예상하지 못한 오류 발생  
**주의점**: 구체적인 오류 정보를 클라이언트에 노출하면 안 됨  
**로깅**: 서버 로그에는 상세한 에러 기록

**상황 예시**:

```javascript
// 데이터베이스 연결 실패 등 서버 내부 오류
GET /api/users

// 응답 (구체적인 오류 내용은 숨김)
HTTP/1.1 500 Internal Server Error
{ "error": "Internal server error occurred" }

// 서버 로그에만 기록: "Database connection failed: Connection timeout"
```

### 502 Bad Gateway

**의미**: 게이트웨이/프록시가 상위 서버로부터 잘못된 응답을 받음  
**실제 상황**:

- 로드밸런서 뒤의 서버가 다운
- API Gateway에서 백엔드 서비스 연결 실패
- CDN에서 원본 서버 접근 실패

**상황 예시**:

```javascript
// Nginx 로드밸런서에서 백엔드 서버가 모두 다운된 경우
GET /api/users

// 응답
HTTP/1.1 502 Bad Gateway
{ "error": "Backend servers are currently unavailable" }
```

### 503 Service Unavailable

**의미**: 서버가 일시적으로 요청을 처리할 수 없음  
**Retry-After 헤더**: 서비스 복구 예상 시간  
**실제 사용**:

- 서버 점검 중
- 트래픽 폭증으로 인한 과부하
- 서버 재시작 중

**상황 예시**:

```javascript
// 서버 점검 중
GET /api/users

// 응답
HTTP/1.1 503 Service Unavailable
Retry-After: 3600
{ "error": "Service maintenance in progress. Please try again in 1 hour" }
```

### 504 Gateway Timeout 

**의미**: 게이트웨이가 상위 서버로부터 시간 내에 응답을 받지 못함  
**실제 상황**:

- 데이터베이스 쿼리 타임아웃
- 외부 API 호출 지연
- 마이크로서비스 간 통신 지연


## 🎯 개발자가 꼭 알아야 할 포인트

### 상태 코드 선택 기준
1. **정확성**: 실제 상황과 일치하는 코드 사용
2. **일관성**: 팀 내에서 동일한 상황에 동일한 코드 사용
3. **보안**: 민감한 정보는 404로 숨기기

### 자주 헷갈리는 구분

- **400 vs 422**: 문법 오류 vs 의미 오류
- **401 vs 403**: 인증 필요 vs 권한 없음
- **502 vs 504**: 잘못된 응답 vs 응답 시간 초과

### API 설계 시 고려사항
- **에러 응답에 상세 정보 포함**: 클라이언트가 문제를 해결할 수 있도록
- **Rate Limiting**: 429와 함께 Retry-After 헤더 제공
- **캐싱**: 304를 활용한 효율적인 캐싱 전략