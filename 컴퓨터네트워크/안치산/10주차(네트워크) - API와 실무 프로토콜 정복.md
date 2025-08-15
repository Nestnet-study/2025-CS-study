### **4주차: API와 실무 프로토콜 정복** 💻

* **REST API** & **RESTful** (설계 원칙) vs **SOAP**
* **쿠키** vs **세션** (동작 방식, 차이점, 용도)
* **인증(Authentication)** vs **인가(Authorization)**
* **OAuth**와 **소셜 로그인** (개념, 흐름)
* **SOP**와 **CORS** (등장 배경, Preflight Request)
* **WebSocket** (특징, HTTP와의 차이점, 사용 사례)

- [[졸작 11주차 - 로그인 고도화]]

---

- ? RESTful API의 6가지 설계 원칙
    - ? HATEOAS 원칙이 왜 중요하며 실무에서 가장 잘 지켜지지 않는 이유는 무엇일까요?
- ? JWT(JSON Web Token)를 이용한 인증 방식은 쿠키/세션 방식과 어떤 차이가 있나요?
- ? 서버를 여러 대로 확장(Scale-out)했을 때 세션 불일치 문제가 발생할 수 있습니다. 이 문제를 어떻게 해결할 수 있을까요?
    - 스티키 세션(Sticky Session), 세션 클러스터링(Session Clustering), 세션 스토리지(Redis 등) 분리 등
- ? 사용자 역할(Role) 기반의 인가 시스템을 설계한다면 어떻게 구현하시겠어요?
    - _의도_: 개념 이해를 넘어 실제 시스템 설계 및 구현 능력을 평가합니다. RBAC(Role-Based Access Control) 모델을 언급하며 User, Role, Permission 테이블 구조를 설명하거나, 인터셉터(Interceptor)나 필터(Filter)를 활용한 권한 체크 로직을 설명할 수 있어야 합니다.
- ? 세션이 서버의 부하를 증가시키긴 하지만 보안 측면에서는 JWT에 비해서 안전할까?'
- ? 대규모 사용자를 지원하는 분산 시스템 환경에서, 세션 기반 인증 방식이 가지는 한계점과 이를 토큰 기반 인증(예: JWT)이 어떻게 극복할 수 있는지 아키텍처 관점에서 설명해 주세요.
- ? 로그인 기능을 구현할때 고려해야하는 보얀취약점에는 어떤게 있을까?
    - ? JWT(JSON Web Token)를 사용한 인증 시스템을 설계할 때, 토큰을 쿠키에 저장하는 방식과 `Authorization` 헤더에 담아 보내는 방식의 장단점은 무엇일까요?
    - ? 해당 보안취약점을 방지하기 위해서는 로그인 기능을 어떻게 구현하면 좋을까요?
- ? SOP는 무엇이며 왜 필요한 정책인가요?
- ? CORS의 동작 원리를 설명하고, 브라우저와 서버가 어떤 헤더를 주고받는지 설명

- ? HTTP Polling, HTTP Long Polling, Websocket, SSE(Server-side Event), WebRTC, Graphql Subscribtion을 비교해서 설명하시오

- ? 모바일 환경처럼 네트워크 연결이 불안정한 상황에서 WebSocket 연결을 안정적으로 유지하기 위해 어떤 전략들을 사용할 수 있을까요?
    - Heartbeat (Ping/Pong) 메커니즘
    - 자동 재연결과 Exponential Backoff
    - 메시지 큐와 상태 동기화
    - 메시지 수신 확인

    - Socket.IO와 같은 라이브러리는 이러한 기능들을 내부적으로 모두 구현해둠

- ? HTTP/3에서도 여전히 Websocket이 사용될까?
    - 연결 수립만 HTTP/3로 하고 이후 Websocket 사용
        - ? 
    - WebSocket을 대체하는 WebTransport 개념이 제시됨
