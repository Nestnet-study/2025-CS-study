# SOP와 CORS

## 1. SOP (Same-Origin Policy, 동일 출처 정책)

SOP는 같은 Origin 에서만 리소스를 공유할 수 있다는 웹 브라우저의 근본적인 보안 정책이다. 여기서 Origin은 URL의 세 가지 요소로 결정된다.

1. **프로토콜 (Protocol):** `http://`, `https://://`
2. **호스트 (Host):** `example.com`, `sub.example.com`
3. **포트 (Port):** `:80`, `:443`, `:3000`

- 이 세 가지가 모두 같아야 Same-Origin 으로 인정된다. 하나라도 다르면 Corss-Origin 으로 간주된다.

| 기준 URL: `https://example.com:8080/index.html` | 비교 URL                               | 동일 출처 여부 | 이유                                |
| ----------------------------------------------- | -------------------------------------- | -------------- | ----------------------------------- |
| 프로토콜                                        | `http://example.com:8080/`             | **No**         | 프로토콜 다름 (`https` ≠ `http`)    |
| 호스트                                          | `https://api.example.com:8080/`        | **No**         | 호스트(도메인) 다름                 |
| 포트                                            | `https://example.com/`                 | **No**         | 포트 다름 (`:8080` ≠ `:443` 기본값) |
| 경로                                            | `https://example.com:8080/mypage.html` | **Yes**        | 프로토콜, 호스트, 포트 모두 동일    |

SOP는 매우 중요하다. 하나의 웹 브라우저로 여러 사이트에 접속하는 일이 빈번하기 때문이다. 예를 들어 은행 사이트에 로그인하고 브라우저에는 인증 정보가 저장된다고 생각해보자. 만약 이 상태에서 어떤 악성 사이트에 접속하면 그 사이트가 사용자의 브라우저를 통해 은행 사이트의 API를 호출하여 모든 정보를 빼돌릴 수 있게 된다.

---

## 2. CORS (Cross-Origin Resource Sharing, 교차 출처 리소스 공유)

프론트 엔드 서버와 API 서버의 도메인이 다른 경우, 외부의 API를 호출하는 경우 등의 상황 때문에 다른 Origin의 리소스를 가져와야 할 일이 매우 빈번하다. 그래서 SOP의 원칙은 유지하되, 신뢰할 수 있는 다른 Origin의 리소스 요청은 예외적으로 허용하는 것이 CORS이다.

CORS의 동작 방식은 크게 두 가지로 나뉜다.

1. Simple Request
   - GET, HEAD, POST 등 간단한 요청은 다음과 같이 동작한다.
   1. 브라우저는 서버에 요청을 보낼 때 자동으로 Origin 헤더에 현재 요청을 보내는 출처를 담아 보낸다.
   2. 서버는 이 요청을 받고, 응답을 보낼 때 `Access-Control-Allow-Origin` 헤더에 리소스 접근을 허용하는 Origin을 명시하여 응답한다.
   3. 브라우저는 자신이 보낸 Origin 헤더 값과 서버가 응답한 `Access-Control-Allow-Origin` 헤더의 값을 비교한다.
2. Preflight Request
   - 실제 요청에 영향을 줄 수 있는 PUT, DELETE 등의 경우 브라우저는 안전을 위해 본 요청을 보내기 전 미리 “이런 요청을 보내도 될까요?” 라는 의미의 Preflight를 보낸다.
   1. 브라우저는 OPTIONS 메소드를 사용하여 Preflight 요청을 보낸다. 이 요청에는 다음의 헤더가 포함된다.
      - `Origin`: 요청을 보내는 출처
      - `Access-Control-Request-Method`: 실제 요청에서 사용할 HTTP 메소드 (e.g., `PUT`)
      - `Access-Control-Request-Headers`: 실제 요청에서 사용할 커스텀 헤더
   2. 서버는 Preflight 요청을 받아 자신의 CORS 정책에 따라 허용하는 메소드와 헤더 등을 응답 헤더에 담아 보낸다.
      - `Access-Control-Allow-Origin`: 허용하는 출처
      - `Access-Control-Allow-Methods`: 허용하는 메소드 목록
      - `Access-Control-Allow-Headers`: 허용하는 헤더 목록
   3. 브라우저는 예비 응답 요청을 보고, 실제 보낼 요청을 보낸다
