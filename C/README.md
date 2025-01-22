# 부록 C. API 설계자를 위한 필딩 논문 가이드

> 발표일 `25.01.22`
>
> 발표자 `박지환`

## 목차

1. 웹의 구조적 특성
2. REST 구조적 제약
3. 질문

## 1. 웹의 구조적 특성 (웹이 성공한 이유)

1. 낮은 진입 장벽
   - 표준 HTTP 프로토콜
   - 클라이언트 & 서버 상호작용이 명확
     <img src="./images/low_wall.png" width="300">
2. 확장성
   - 클라이언트-서버 분리 & 무상태성 -> 트래픽 증가에 대응하기 쉬움
     <img src="./images/stateless.png" width="300">
3. 분산 하이퍼미디어
   - HATEOAS: 동적으로 API를 탐색 가능
   - 서버의 구조나 추가 엔드포인트를 알 필요가 없음
     <img src="./images/hypermedia.png" width="300">
4. 인터넷-규모
   - "무질서한 규모 확장성": 시스템의 다른 각 부분 사이의 장기적인 관계 X.
   - "독립적인 배포": 장기적인 관계가 없으므로 시스템의 각 부분이 다른 속도로 변화 O.
     <img src="./images/internet.png" width="300">

## 2. REST 구조적 제약

1. 클라이언트 - 서버
   - 장점: 명확한 역할 분담을 통한 유지보수성 향상
   - 경쟁자: 이벤트 기반 통합 아키텍처 (Web Sockets)
   - 목적: 역할 분담을 통해 유지보수를 쉽게 만들자
2. 상태 없음 (Stateless)
   - 장점: 서버가 클라이언트의 상태를 저장하지 않으므로 확장성 향상
   - 예시: Session Id는 서버에 저장하면 안 됨
   - 목적: 서버의 확장을 어렵지 않게 하자
3. 캐싱
   - 장점: 기존 요청의 응답에 매치시켜 네트워크 데이터 송수신 비용 최소화
   - 특징: 자기 서술형 메시지 제약 (응답에 모든 정보가 있음) & 상태 없음 제약 (각 요청은 독립적) 덕분에 캐싱이 가능
   - 목적: 비용을 줄이자
4. 단일 인터페이스
   - 장점: HTTP 표준을 활용하여 아키텍쳐 단순화
   - 예시: 자원은 URI를 통해 고유하게 식별, HTTP 메서드 활용, 리소스 표현 활용
   - 목적: API 구조를 뻔하게 만들자
5. 계층화 시스템
   - 장점: 중개자 (프록시/게이트웨이)를 사용하여 확장성을 향상
   - 예시: 한 게이트웨이가 받은 HTTP 요청을 FTP 서버에서 파일을 다운로드하고 HTTP 응답으로 보내줌
   - 목적: 구조화를 하자
6. 코드 온 디맨드 (선택적 제약 조건)
   - 장점: 클라이언트의 기능을 동적으로 확장
   - 단점: 서버에 종속하는 구조, 보안 이슈, 디버깅 어려움

## 3. 질문

### Q1.

> 이창준 \
> Authorization 헤더의 인증방식이 궁금합니다. 어떤 보안 프로토콜을 사용하는지, 그 보안 프로토콜을 설정하는게 가능한지? p.390

### A1.

문법: `Authorization: <type> <credentials>`

`<type>`에 따라 인증 체계가 다름: https://developer.mozilla.org/ko/docs/Web/HTTP/Authentication#%EC%9D%B8%EC%A6%9D_%EC%8A%A4%ED%82%A4%EB%A7%88

### Q2.

> 안재우 \
> 410p 에서 User-Agent가 Mozilla가 아닌 브라우저들은 필요한 표현을 받지 못할 수 있다고 하는데, 이에 대한 예시로는 어떤 것들이 있을까요? 책에서 말하는 Mozilla인데 IE인것처럼 User-Agent를 설정하는 예시처럼 Mozilla가 아닌 다른 User-Agent가 더 유리한 경우도 이에 대한 예시들이 있을지 궁금합니다

### A2.

https://github.com/sweelemk/kilix.info/blob/40749f5225506862ba27ba35c05c9349c418e76b/js/common.js#L338

https://github.com/WPPlugins/eht-visits/blob/6188623ffa6f056eec7c6d2808bfe757cc9eacc7/Visits.php#L405

### Q3.

> 정시우 \
> Cookie는 Rest의 무상태성의 원칙을 위배하기 때문에 권장되지 않습니다. 하지만 그럼에도 cookie를 사용해야 하는 상황에 대해서 소개해주세요.

### A3.

https://www.cloudflare.com/ko-kr/learning/privacy/what-are-cookies/

### Q4.

> 박지환 \
> 아마존이 S3 API를 위해 정의한 X-amz-acl과 X-amz-date 헤더들은 어떤 특징을 가지고 있는지, 그리고 다른 헤더들과 비교했을 때 결정적인 차이점은 무엇인지 궁금합니다.

### A3.

[x-amz-acl](https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/userguide/example-bucket-policies-condition-keys.html): 버킷 소유자에게 특정 권한을 부여하는 데 사용
