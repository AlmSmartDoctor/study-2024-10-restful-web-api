# 11장. API를 위한 HTTP (인증~)
> 발표일 `24.1.2`
>
> 발표자 `서형석`

## 목차

1. 인증
2. HTTP 확장
3. 질문

---

## 인증

두 단계가 존재

1. 사용자가 자신의 증명을 서버에 제공하는 것
    - 사람이 웹 브라우저를 통해 API 서버에 계정을 만들거나,
    - 기존 웹 사이트에 만든 계정을 API 서버에 등록
2. 사용자의 증명을 각 API를 요청할 때마다 자동으로 첨부하는 것
    - 왜 모든 HTTP 요청에 증명을 포함해야 할까? -> 상태가 없는 제약 조건 때문

WWW-Authenticate, Authorization 헤더

```
GET / HTTP/1.1
Host: api.example.com
```

```
401 Unauthorized HTTP/1.1
WWW-Authenticate: Basic realm="My API"
```
WWW-Authenticate 헤더는 서버가 어떠한 종류의 인증을 수락하는지 설명.

서버는 클라이언트가 HTTP 기본 인증을 사용하길 원함.

```
GET / HTTP/1.1
Host: api.example.com
Authorization: Basic YWxpY2U6cGFzc3dvcmQ=
```

### 기본 인증

- RFC 2617에 명시
- 단순한 사용자 이름 / 비밀번호 체계
- 두 가지 문제점
    1. 안전하지 않음
        - Base64라는 단순하고 역변환 가능한 변환을 통한 alice:password 문자열
        - HTTPS를 사용하면 해결. SSL이 모든 요청과 응답을 암호화
        - Digest라는 두 번째 인증 방법을 쓰면 HTTPS 없이도 해결. 여기서는 소개 X
    2. 클라이언트 하나만 블락할 수 없음
        - 트위터 API와 같은 유명한 API에 클라이언트 10개가 연결
        - 한 클라이언트가 스팸을 보냄
        - 하나를 무력화시키기 위해 비밀번호를 변경하면 열 개가 모두 망가짐
- 처음부터 각 클라이언트에 다른 증명을 줄 수 있다면?

### OAuth1.0

![image](https://github.com/user-attachments/assets/a3a3212d-ceb9-44b6-b2cc-835fcb8dd05d)

단계
1. 도메인 서버는 비밀스럽게 API 제공자인 api.example.net으로 임의의 증명을 요청
2. 도메인 서버는 HTTP 리다이렉트를 앨리스의 브라우저로 보냄. 앨리스는 사용중인 웹 사이트를 떠나고 API 제공자가 보내준 웹 페이지 Example.net으로 이동
- 앨리스가 Example.net에 로그인 되어 있지 않다면, 로그인하거나 계정을 생성해야함. 비밀번호를 입력해야 하나, Example.net의 비밀번호를 api.example.net에 주는 것이지, 도메인 서버에 주는 것은 아님

![image](https://github.com/user-attachments/assets/bf9a17b8-e145-4581-acfc-db12bf33ea0a)

3. 로그인 후, 앨리스는 1단계에서 취득한 임시 증명과 연결된 웹 페이지를 봄. 페이지는 무엇이 진행되는지 앨리스에게 설명하고, 그녀가 api.example.net의 토큰 증명 집합을 도메인 서버에 허용하길 원하는지 물음
4. 앨리스가 결정을 하고 브라우저는 원래 사용중이던 페이지로 리다이렉트됨
5. 4단계에서 No라고 결정하면 클라이언트는 api.example.net의 토큰 증명서를 앨리스로부터 받지 못함. 4단계에서 yes라고 결정하면 클라이언트는 1단계에서 취득한 임시 증명서를 실제 토큰 증명서로 교환할 수 있음.
- 다음과 같은 요청에서 Authorization 헤더를 생성해서 HTTP 요청을 암호화하여 서명하는 데 사용할 수 있음
```
GET / HTTP/1.1
Host: api.example.net
Authorization: OAuth realm="Example API",
    oauth_consumer_key="rQLd1PciL0sc3wZ",
    oauth_signature_method="HMAC-SHA1",
    oauth_timestamp="1363723000",
    oauth_nonce="JFI8Bq",
    oauth_signature="4HBjJvupgIYbeEy4kEOLS%Ydn6qyV%UY"
```

이점
- 앨리스가 믿을 수 없는 열 개의 다른 소프트웨어에 비밀번호를 모두 알려줘야 하는 일을 막아줌
- 진행 과정 중 클라이언트가 하는 무언가가 맘에 안들면 앨리스는 토큰 증명서를 폐기할 수 있음
- API 제공자가 클라이언트 소프트웨어 일부분에서 예상치 못한 행동을 감지하면 제공자는 클라이언트 증명서를 폐기할 수 있음(oauth_consumer_key)
- Authorization 헤더에 있는 oauth_timestamp와 oauth_nonce 값은 '재생 공격'을 막음
    - 공격자가 앨리스의 요청을 엿보고 있다가 흉내 내기 위한 동일한 요청을 만드는 것


#### OAuth1.0의 부족한 점

OAuth1.0은 웹 브라우저 안에서 발생할 때 잘 동작. 앨리스가 데스크탑 애플리케이션을 사용한다면?

- 2단계에서 api.example.net에 있는 페이지로 앨리스를 리다이렉트하는 대신 데스크탑 애플리케이션이 새 브라우저 창을 열어 이 페이지를 열어야 함
- 앨리스가 4단계에서 결정하고 나면 api.example.net에서 앨리스를 어디로 리다이렉트 시켜야 할지 정보가 없음.
    - 백그라운드에서 데스크톱 애플리케이션은 앨리스가 임시 증명서를 수락했는지에 대해 api.example.net에게 지속적으로 물어봐야 함.

- 5단계 과정이 부족하거나 전혀 동작하지 않은 상황
    - 앨리스가 휴대 전화 앱을 사용하거나 게임 콘솔에서 게임하는 경우. 웹 브라우저가 없는 경우
    - 앨리스가 api.example.net을 위한 클라이언트 소프트웨어를 작성한 사람일 경우
    - 앨리스가 데스크톱 애플리케이션을 사용 중인데 웹 브라우저 안에서 실행되는 경우

---

### OAuth2.0

- RFC 6749에 정의
- OAuth 엑세스 토큰을 갖기 위해 네 가지 다른 과정을 정의
    - 승인 코드 제공 (OAuth1.0과 동일)
        - 리소스 소유자(앨리스)는 '인증 서버'를 통해 인증
    - 암묵적 승인
        - 웹 브라우저 안에서 동작하는 애플리케이션에 좋음
        - 액세스 토큰이 포함된 URL로 리다이렉트
    - 리소스 소유자 비밀번호 증명서
        - 앨리스가 Example.net의 사용자 이름과 비밀번호를 클라이언트에 제공해 OAuth 액세스 토큰으로 교환
        - 악성 클라이언트는 앨리스의 비밀번호를 훔칠 수 있음.
        - 그러나 정상적인 클라이언트는 OAuth 액세스 토큰을 갖는대로 앨리스의 비밀번호를 잊음
    - 클라이언트 증명서
        - 앨리스가 사용하는 클라이언트 작성자 자신일때 좋음
        - 앨리스가 클라이언트를 api.example.net에 등록할 때, example.net 계정에 자유롭게 접근할 수 있는 권한을 클라이언트에 주는 증명서를 자동으로 받는다.
- 모바일 애플리케이션을 위한 백엔드로 제공하기 위한 API를 작성 중이라면 '리소스 소유자 비밀번호 증명서'과정만 구현할 수 있음


---

## HTTP 확장

세 가지 주요 프로토콜을 정의. (하나는 CoAP로, 13장에서 소개)

WebDAV와 HTTP2.0을 HTTP 메서드의 몇 가지 확장과 함께 다룸

### PATCH 메서드

- HTTP PUT의 성능 문제 해결: 변경을 원하는 표현만 보내도록 함
- 새로운 미디어 유형에 동의해야만 한다는 단점: 이미 다 해 놓음.

### LINK와 UNLINK 메서드

존재하는 항목을 컬렉션에 더하는 요청
```
LINK /collections/a6o HTTP/1.1
Host: www.example.com
Link: <http://www.example.com/items/4180>;rel="item"
```
리소스 체인에서 두 번째 리소스를 제거하는 요청
```
UNLINK /story/part1 HTTP/1.1
Host: www.example.com
Link: <http://www.example.com/story/part2>;rel="next"
```

요청이 실행된 후 리소스들은 여전히 존재하나, 연결 관계가 "next"인 연결이 더 이상 없다.

PUT으로 이러한 기능을 만들 수 있으나, 작업을 단순화한다. (하이퍼미디어 연결의 조작이라는 공통 작업을 분리하고 프로토콜 의미 체계를 부여)



### WebDAV
파일 시스템 작업을 위해 HTTP/1.1을 확장한 프로토콜

특징
- 목표: 원격 파일 시스템에 파일과 디렉터리를 위한 HTTP 리소스들을 편하게 게시
- 사용처: 마이크로소프트 Sharepoint와 Subversion 버전 관리 시스템.
- 잘 안 쓰임
    - 아마존 S3, 드랍박스 등에서 쓰지 않음.
    - AtomPub처럼 API가 어떤 형태를 지녀야 하는지의 관점이 요즘 관점과 달라 무시됨

HTTP 메서드 정의

| HTTP 메서드 |  역할               |
| --------- | ------------------ |
| MKCOL     | 로컬 파일 시스템에서 디렉터리처럼 행동하는 '컬렉션' 리소스를 정의 |
| PROPPATCH, PROPFIND | 리소스의 속성을 수정, 컬렉션에서 특정 속성을 가진 리소스 서치 |
| LOCK, UNLOCK | 명시적으로 리소스를 잠그는 것을 허용 |
| MOVE, COPY | 리소스의 URL을 변경, 리소스의 현재 표현을 다른 URL로 복사

상태 코드 정의
- 423(Locked), 507(Insufficient Storage)
    - WebDAV이외에도 쓰일 수 있으나 추천하지 않음. 423(Locked)대신 409(Conflict)를 쓸 수 있음


### HTTP 2.0

- SPDY에 기반(구글에 의해 기업 표준으로 정의). HTTP에 성능 계층을 추가한 것
- HTTP 1.1을 사용하고 있는 것처럼 보일 수 있어야 함.
- 호환 계층이 자동으로 HTTP 1.1과 HTTP 2.0 사이를 변환할 수 있어야 함

---

## 질문

### Q1.
> 사용자가 비밀번호를 변경하거나 계정의 인증을 무효화하고 싶을 때 어떤 과정을 통해 클라이어트에 제공된 토큰을 무효화할 수 있나요?
>
> 최준영

### A1. 

토큰 저장 방식 (Stateless - JWT 토큰, Stateful - 세션 기반 토큰)에 따라 다름.

### Stateless 토큰(JWT)의 경우

a. 블랙리스트 활용

- 블랙리스트를 생성:
    - JWT의 jti(JWT ID) 또는 전체 토큰 값을 저장하는 블랙리스트를 설정.

- 토큰 확인 시 블랙리스트 검증:
    - 인증 과정에서 토큰의 jti를 블랙리스트와 비교하여 무효화 여부를 판단.

b. 토큰의 유효기간 활용

- iat(issued at) 기반 무효화:
    - 사용자가 비밀번호를 변경하거나 인증을 무효화하면, 사용자 계정의 tokenInvalidationTime 값을 업데이트.
    - 인증 검증 시, 토큰의 iat가 tokenInvalidationTime 이후라면 토큰을 무효화.

c. 새 키 발급

- JWT 서명 키 재생성:
    - JWT의 서명 키를 변경하면 기존 토큰은 모두 무효화됨.
    - 이 방법은 전 계정에 영향을 미치므로 신중히 사용.

### Stateful 토큰의 경우

1. 세션 무효화:
- 서버의 세션 저장소(예: Redis, DB)에서 해당 세션을 삭제.
- 사용자가 다른 기기에서 로그아웃하거나 세션이 만료됨.

2. 데이터베이스에서 토큰 관리:

- 사용자별로 토큰을 저장하는 테이블(예: user_tokens)에서 해당 토큰을 삭제.
- 이후 인증 요청이 들어올 때 해당 토큰이 없으면 인증 실패 처리.

---

### Q2.

> RESTful 서버 구현에서 세션 데이터가 없어야 하는 이유와 이를 사용하게 될 경우 생길 수 있는 문제점, 그럼에도 사용해야 할 경우가 있는 지 궁금합니다.
>
> 곽재영

### A2.

### 1. 세션 데이터가 없어야 하는 이유 - Stateless의 장점

확장성
- 상태를 저장하지 않으면, 요청 처리가 서버에 독립적이므로 부하 분산(Load Balancing)과 서버 확장이 쉬워집니다.
- 클라이언트가 어느 서버로 요청을 보내더라도 일관된 응답을 받을 수 있습니다.

단순성
- 상태 관리 로직을 줄이면서 서버 구현이 간단해지고 유지보수가 용이합니다.
- 복잡한 상태 동기화 작업이 필요하지 않습니다.

복원력
- 서버 장애가 발생해도 상태 데이터 복구가 필요 없으므로, 서버가 신속히 재가동될 수 있습니다.

### 2. 세션 데이터를 사용할 때의 문제점

a. 확장성 저하
- 세션 데이터가 특정 서버에 저장되면, 요청을 동일한 서버로 라우팅해야 하므로 부하 분산이 어려워집니다.
    - 이를 해결하기 위해 세션 데이터를 Redis 같은 중앙 저장소에 저장해야 하지만, 이는 추가적인 복잡성과 비용을 유발합니다.

b. 성능문제
- 서버는 세션 데이터를 관리하기 위해 메모리 또는 외부 스토리지(예: 데이터베이스, Redis)를 사용해야 하므로 요청 처리 속도가 느려질 수 있습니다.

c. 복잡성 증가
- 클라이언트의 상태를 지속적으로 관리해야 하므로 코드의 복잡성이 증가합니다.
- 상태 동기화 및 만료 관리(예: 세션 만료, 갱신)가 필요합니다.

d. 장애 복구 어려움
- 서버에 세션 데이터가 저장된 경우, 해당 서버가 중단되면 세션 데이터가 손실될 위험이 있습니다.

### 3. 그럼에도 세션 데이터를 사용해야 하는 경우

a. 상태를 유지해야 하는 애플리케이션

1. 복잡한 사용자 인터페이스:
- 사용자가 여러 단계를 거치는 워크플로우를 수행해야 할 때, 상태를 유지하는 것이 유용합니다(예: 쇼핑몰의 장바구니).

2. 다단계 인증(Multi-Factor Authentication):
- 인증 프로세스 중간에 상태를 저장해야 할 경우 세션이 필요할 수 있습니다.

b. 보안상의 이유
- 사용자가 민감한 작업(예: 은행 거래)을 수행하는 동안 세션 데이터를 사용하여 사용자를 보호할 수 있습니다.
    - 세션 기반 토큰 관리로 인증 상태를 지속적으로 확인 가능.

c. 클라이언트의 제약 사항
- 클라이언트가 토큰을 관리할 수 없는 경우(예: IoT 디바이스 또는 특정 제한이 있는 환경).

d. 서버 중심 로직 구현
- 상태를 서버에서 관리해야만 일관성이 유지되는 비즈니스 로직이 있을 때.


---

### Q3.

> OAuth 2.0 대신 OAuth 1.0을 선택해야 하는 상황이 있을까요?
>
> 박근영

### A3.

1. HTTPS 사용이 불가능한 환경
- OAuth 1.0은 요청 서명 방식으로 보안을 유지하므로, HTTPS 없이도 안전하게 사용할 수 있습니다.
- 예: 제한적인 네트워크 환경에서 HTTPS 지원이 불가능한 경우.

2. 높은 수준의 요청 보안이 필요한 경우
- 요청 데이터가 민감하고 변조를 방지해야 하는 경우 OAuth 1.0의 서명 방식이 유용합니다.
- 요청 서명은 메시지 자체의 무결성을 보장하며, 요청을 감청하거나 변조하려는 시도를 방지합니다.

3. 레거시 시스템과의 호환성
- 기존에 OAuth 1.0을 사용하는 시스템과 통합해야 하는 경우.
- OAuth 2.0으로 전환이 어렵거나 불필요한 경우.

4. 신뢰할 수 없는 클라이언트와의 통신
- 신뢰할 수 없는 환경에서 통신할 때, OAuth 1.0은 요청마다 서명을 생성해 더 안전한 인증 방식을 제공합니다.
- OAuth 2.0의 Bearer Token은 요청 간 복사 및 재사용 위험이 있어, 이와 대비해 OAuth 1.0이 더 적합할 수 있습니다.

5. 클라이언트 비밀 관리가 어려운 환경
- OAuth 2.0은 Client Secret을 안전하게 관리해야 하지만, 일부 환경에서는 비밀 유지가 어려울 수 있습니다.
- OAuth 1.0은 비밀 키를 사용하지만, 요청마다 새 서명을 생성하므로 복제 가능성이 줄어듭니다.

---

### 04. 

> HTTP/1.1, HTTP/2, HTTP/3의 동작 구조가 어떻게 변해왔는지, 그리고 각각의 장단점이 무엇인지 궁금합니다
>
> 한태혁

### A4.

### HTTP/1.1

구조 및 동작
- 텍스트 기반 프로토콜.
- 요청-응답(Request-Response) 모델로 작동.
- 연결당 하나의 요청을 처리(기본적으로 직렬화된 요청).
- Keep-Alive를 지원하여 하나의 연결을 재사용 가능.

장점
- 단순성: 설계와 구현이 상대적으로 단순.
- 범용성: 오랜 시간 동안 표준으로 사용되어 폭넓은 지원.
- Keep-Alive: 연결을 재사용해 성능 향상.

단점
- 헤더 중복: 요청마다 반복적으로 동일한 헤더 전송.
- HOLB 문제: 연결당 한 번에 하나의 요청만 처리, 앞선 요청이 지연되면 뒤 요청도 지연됨(Head-of-Line Blocking).
- 비효율성: 많은 작은 리소스 요청 처리 시 성능 저하.


### HTTP/2

구조 및 동작
- 이진(Binary) 기반 프로토콜.
- 단일 연결에서 다중화(Multiplexing) - 지원: 여러 요청과 응답이 동시에 처리 가능.
- 헤더 압축: HPACK 알고리즘으로 헤더 크기를 줄임.
- 서버 푸시(Server Push)를 통해 클라이언트 요청 없이도 리소스를 미리 전송.

장점
- 다중화: 단일 TCP 연결에서 여러 요청/응답 병렬 처리.
- 헤더 압축: 대역폭 절약.
- 서버 푸시: 추가 요청을 줄여 성능 개선.
- 속도 향상: HOLB 문제 완화.

단점
- TCP 기반: 패킷 손실 시 모든 스트림이 영향을 받음.
- 복잡성 증가: HTTP/1.1보다 구현이 복잡.
- HOLB 문제 완전 해결 X: TCP 연결 수준에서 여전히 존재.


### HTTP/3

구조 및 동작
- QUIC 기반: UDP 위에서 작동.
- 연결과 스트림이 독립적, 패킷 손실이 한 스트림에만 영향.
- 연결 설정이 빠름: TLS 핸드셰이크와 전송 계층 연결 통합.

장점
- HOLB 문제 해결: 스트림 간 독립적 처리.
- 빠른 연결 설정: QUIC의 초기 연결 성능으로 지연 감소.
- 모바일 친화적: 네트워크 변경(예: Wi-Fi ↔ LTE)에도 연결 유지.
- 효율적 데이터 전송: 헤더 압축과 다중화 유지.

단점
- 지원 제한: 상대적으로 새로운 기술, 모든 환경에서 지원되지 않을 수 있음.
- UDP 의존성: UDP 성능이 낮거나 차단된 네트워크에서는 사용 불가.
- 복잡성: 구현 및 디버깅이 더 복잡.


---

### 05.

> RFC 6902와 RFC 7396은 공통적으로 HTTP Patch 메서드를 다루는데,
클라이언트 입장에서 각각의 방식을 사용할때 어떤 차이가 있는지 설명해주시면 감사하겠습니다.
>
> 임해찬

### A5.

#### RFC 6902 vs RFC 7396: 주요 차이점

| 특성	| RFC 6902 (JSON Patch)	| RFC 7396 (JSON Merge Patch) |
| --- | ---                    | -------------------------- |
| 작동 방식	| 명령 기반 (Operation 기반 수정) |	상태 기반 (최종 상태를 병합) | 
| 데이터 형식 | 명령 배열 ([{op, path, value}])	| JSON 문서 
| 삭제 방식 | remove 명령	| 값을 null로 설정 |
| 복잡성 |	높은 편 (명령 작성 및 경로 지정 필요) |	낮은 편 (수정할 속성만 전송) |
| 지원 연산	| 복잡한 작업 지원 (add, replace, move, copy, test 등)	|단순 작업만 지원 (add, update, delete) |
|성능 최적화 |	특정 경로만 수정하므로 데이터 전송량 최소화 가능 |	전송량 최소화 가능하나, 일부 중첩 구조에서 추가 데이터 전송 |
| 주요 사용 사례 | 복잡한 JSON 객체의 세부 수정	| 단순한 속성 추가, 수정, 삭제 | 

#### 클라이언트 입장에서의 선택 기준
1. 단순성 요구:

- 단순 속성 수정이 많다면 RFC 7396(JSON Merge Patch).
- 예: 기본 CRUD 작업.

2. 정밀 제어 필요:
- 복잡한 JSON 구조에서 미세 조정이 필요하면 RFC 6902(JSON Patch).
- 예: 리스트 항목 추가/삭제, 특정 조건에 따른 수정.

3. 서버 지원 여부:
- 서버가 지원하는 방식에 따라 선택.

4. 데이터 전송량:
- JSON Patch는 전송량이 더 최적화될 가능성이 큼.