# 웹서버와 WAS

웹서버(Web Server)는 **정적인 콘텐츠**를, WAS(Web Application Server)는 **동적인 콘텐츠**를 처리하는 서버이다.

## 1. 웹서버 (Web Server)

HTML, CSS, JS 파일과 같이 요청에 따라 저장된 정적 콘텐츠를 그대로 전달한다. Nginx, Apache가 그 예시이다.

---

### 2. WAS (Web Application Server)

사용자마다 다른 내용을 보여주는 콘텐츠인 동적 콘텐츠를 제공하는 역할이다. 데이터베이스 조회, 비즈니스 로직 처리가 필요한 모든 요청은 WAS를 통한다.

Tomcat이 그 예시이다.

---

## 3. 웹서버와 WAS

요즘 웹사이트는 정적 콘텐츠와 동적 콘텐츠가 섞여 있다. 이 둘을 분산 처리하기 위해 웹서버와 WAS를 모두 사용하는 추세이다.

즉, 현대의 일반적인 웹 서비스 구조는 다음과 같다.

> 사용자 ↔️ 웹서버 (Nginx/Apache) ↔️ WAS (Tomcat 등) ↔️ 데이터베이스
