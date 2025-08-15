

## **​DNS (Domain Name System) 란?**

도메인 네임 시스템 (Domain Name System, DNS) 은 **호스트의 도메인네임 (www.example.com)을 네트워크주소(192.168.1.0)로 변환**하거나, 그 반대의 역할을 수행하는 시스템이다.

예를 들면 우리가 자주 접하는 naver.com , google.com 모두 DNS을 가진 DN(Domain Name)이라고 할 수 있다.

이들은 사실 **문자열의 탈을 쓴 IP**이다.

---

## DNS 서버 구조


![[Pasted image 20250730133404.png]]


#### 1. 기지국 DNS 서버

인터넷 설치 시 각 통신사마다 제공하는 DNS 서버

- KT, SK, LG 등 통신사별로 고유한 DNS 서버 IP 주소 보유
- 도메인 조회 시 가장 먼저 접근하는 DNS 서버

#### 2. Root DNS 서버

최상위 DNS 서버로 모든 DNS 서버가 기본적으로 주소를 갖고 있음

- 트리 구조의 최상단에 위치하여 전체 DNS 시스템의 시작점 역할
- 해당 도메인 정보가 없으면 TLD 서버 주소를 제공

#### 3. TLD 서버 (Top-Level Domain, 최상위 도메인 서버)

루트 도메인 바로 아래 단계의 1단계 도메인을 관리

- .com, .net, .org 등 일반 최상위 도메인과 .kr, .jp 등 국가 최상위 도메인으로 구분
- 도메인의 확장자(.com, .net 등)를 보고 해당하는 Second-level DNS 서버 주소 제공

###### 주요 TLD 종류

- **.com**: 영리 목적의 기업이나 단체
- **.net**: 네트워크 관리 기관
- **.org**: 비영리 기관
- **.edu**: 미국 4년제 이상 교육기관
- **.kr**, **.jp**: 국가별 도메인

#### 4. Second-level DNS 서버 (2차 도메인)

TLD 서버에서 넘겨받은 요청을 처리하는 서버

- naver.com, google.com 등에서 앞의 문자열(naver, google)을 담당
- 해당 기업이나 조직이 직접 관리하는 DNS 서버

#### 5. Sub DNS 서버 (최하위 서버)

서브 도메인(www, mail, cafe 등)을 구분하는 최하위 서버

- [www.naver.com](http://www.naver.com), mail.naver.com 등 세부 서비스별 도메인 관리
- 실제 서비스가 운영되는 서버의 IP 주소 정보 보유

##### 네이버 서브 도메인 예시

- **[www.naver.com](http://www.naver.com)**: 네이버 홈페이지
- **mail.naver.com**: 네이버 메일
- **blog.naver.com**: 네이버 블로그
- **cafe.naver.com**: 네이버 카페


---
# DNS 동작 순서


## DNS 쿼리 방식 비교

| 구분               | Recursive 쿼리 (재귀 쿼리)      | Iterative 쿼리 (반복 쿼리)   |
| ---------------- | ------------------------- | ---------------------- |
| **요청 주체**        | 클라이언트 → Local DNS 한 번만 요청 | 클라이언트가 각 DNS 서버에 직접 요청 |
| **처리 방식**        | Local DNS가 모든 조회 작업 대행    | 클라이언트가 단계별로 직접 조회      |
| **응답 내용**        | 최종 IP 주소만 전달              | 다음 DNS 서버 주소를 알려줌      |
| **클라이언트 부담**     | 매우 적음 (1회 요청)             | 많음 (여러 번 요청 필요)        |
| **Local DNS 부담** | 많음 (모든 조회 수행)             | 적음 (1회 응답만)            |
| **네트워크 트래픽**     | Local DNS 중심으로 집중         | 클라이언트와 각 DNS 서버 간 분산   |
| **캐싱 효과**        | Local DNS에서 효율적 캐싱        | 클라이언트별 개별 캐싱           |
| **구현 복잡도**       | 클라이언트 측 단순                | 클라이언트 측 복잡             |
| **실제 사용**        | 일반 사용자 환경                 | 주로 DNS 서버 간 통신         |

### 간략한 설명

**Recursive 쿼리**는 사용자가 웹 브라우저에 도메인을 입력했을 때 일반적으로 사용되는 방식입니다. 사용자는 Local DNS에게 한 번만 요청하면 Local DNS가 모든 복잡한 조회 과정을 대신 처리하여 최종 IP 주소를 전달해줍니다.

**Iterative 쿼리**는 주로 DNS 서버들 간의 통신에서 사용됩니다. 각 DNS 서버는 요청받은 도메인 정보가 없으면 다음에 물어볼 DNS 서버의 주소만 알려주고, 요청자가 직접 그 서버에 다시 요청해야 합니다.

실제 인터넷 환경에서는 **하이브리드 방식**을 사용합니다. 클라이언트와 Local DNS 간에는 Recursive 방식으로, Local DNS와 다른 DNS 서버들(Root, TLD, Authoritative) 간에는 Iterative 방식으로 통신하여 효율성과 성능을 모두 확보합니다.

---
## Recursive 쿼리 동작 


#### 1. 초기 요청

사용자가 브라우저에 [www.naver.com을](http://www.naver.com%EC%9D%84) 입력하면 Local DNS(기지국 DNS 서버)에게 IP 주소를 요청한다.

- 통신사(KT, SK, LG)에서 제공하는 DNS 서버로 인터넷 연결 시 자동 설정됨


#### 2. Root DNS 서버 조회 시작

Local DNS가 다른 DNS 서버들과 통신을 시작하여 먼저 Root DNS 서버에게 IP 주소를 요청한다.

- ICANN이 관리하는 최상위 DNS 서버로 전세계 961개 운영 중


#### 3. Root DNS 응답

Root DNS 서버가 IP 주소를 찾을 수 없어 "다른 DNS 서버에게 물어봐"라고 응답한다.

- Root DNS는 직접적인 도메인 정보는 없고 TLD DNS 서버 정보만 제공


#### 4. TLD DNS 서버 조회

Local DNS가 com 도메인을 관리하는 TLD DNS 서버에 다시 IP 주소를 요청한다.

- 최상위 도메인(.com, .org, .net 등)을 관리하는 서버


#### 5. TLD DNS 응답

com TLD DNS 서버에도 해당 정보가 없어 "다른 DNS 서버에게 물어봐"라고 응답한다.

- TLD 서버도 직접적인 도메인 정보는 없고 Authoritative DNS 정보만 제공


#### 6. Authoritative DNS 서버 조회

Local DNS가 naver.com DNS 서버(Authoritative DNS)에게 IP 주소를 요청한다.

- 실제 도메인과 IP 주소 관계가 저장된 권한 있는 서버


#### 7. 최종 IP 주소 응답

naver.com DNS 서버가 "[www.naver.com의](http://www.naver.com%EC%9D%98) IP 주소는 222.122.195.6"이라고 응답한다.

- Authoritative DNS만이 실제 도메인의 정확한 IP 주소 정보를 보유


#### 8. 결과 전달 및 캐싱

Local DNS가 IP 주소를 캐싱하고 사용자 PC에 전달한다.

- 같은 도메인 재요청 시 빠른 응답을 위해 결과를 저장해둠