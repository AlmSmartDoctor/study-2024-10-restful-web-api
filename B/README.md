# 부록 B. 헤더 목록

## 목차

1. 헤더
2. 커스텀 헤더
3. 질문

## 헤더

HTTP 헤더: HTTP 요청이나 응답 프로토콜 의미 체계를 설명하는 메타데이터

- Accept: MIME 타입으로 표현되는, 클라이언트가 이해 가능한 컨텐츠 타입이 무엇인지를 알려줌

- Content-Encoding: 개체 본문에 어떤 추가적인 컨텐츠 인코딩/압축이 적용될지를 나타냅니다.

- Content-Type: 클라이언트에게 반환된 컨텐츠의 컨텐츠 유형이 실제로 무엇인지를 알려줌

    1. 서버가 Content-Type 결정 후 Content-Encoding 형식으로 압축
    2. 클라이언트가 Content-Encoding 형식으로 압축 해제 후 Content-Type으로 해석

- Set-Cookie: 서버에서 사용자 에이전트로 쿠키를 보내는데 사용

- Cookie: Set-Cookie 헤더와 함께 서버에 의해 이전에 전송되어 저장된 HTTP cookies를 포함

- Cache-Control: 요청과 응답 내의 캐싱 메커니즘을 위한 디렉티브를 정하기 위해 사용

- Last-Modified: 원본 서버가 리소스가 마지막으로 수정되었다고 생각하는 날짜와 시간을 포함

- Etag: 특정 버전의 리소스를 식별하는 식별자

    https://velog.io/@leemember/HTTP-%ED%97%A4%EB%8D%94-%EC%BA%90%EC%8B%9C%EC%99%80-%EC%A1%B0%EA%B1%B4%EB%B6%80-%EC%9A%94%EC%B2%AD-%EC%BA%90%EC%8B%9C-%EA%B8%B0%EB%B3%B8-%EB%8F%99%EC%9E%91

- Location: 리다이렉션의 대상이나 새로 만들어진 리소스의 URL (3xx (redirection) 또는 201 (created) 응답 상태와 함께 제공)

- Content-Location: 반환된 데이터에 대한 대체 위치

- Access-Control-Allow-Origin: 응답이 주어진 origin으로부터의 요청 코드와 공유될 수 있는지를 나타냄 (`Access-Control-Allow-Origin: <origin>`)

- Content-Security-Policy (CSP): 교차 사이트 스크립팅(XSS)과 데이터 주입 공격을 비롯한 특정 유형의 공격을 탐지하고 완화하는 데 도움이 되는 추가 보안 계층

- Strict-Transport-Security (HSTS): 사이트가 HTTPS를 통해서만 접근되어야 하며 향후 HTTP를 사용하여 사이트에 접근하려는 모든 시도는 자동으로 HTTPS로 변환되어야 함을 브라우저에 알림

- Authorization: 서버의 사용자 에이전트임을 증명하는 자격을 포함

- User-Agent: 서버와 네트워크 피어가 요청하는 애플리케이션, 운영체제, 제조업체, 사용자 에이전트 요청 버전을 식별하는 독특한 문자열

## 커스텀 HTTP 헤더

새로운 HTTP 헤더를 만든다면 기존 HTTP 헤더처럼 문서를 만들어야 한다.

원래 확장(extension)을 의미하는 X-로 커스텀 헤더 이름을 시작했는데, RFC 6648에서는 그냥 X- 빼도록 설명되어있다.
=> 만약 커스텀 헤더가 표준화되었을 때 X-를 사용하던 기존 클라이언트에 영향을 줄 것이다

- X-amz-*: AWS에서 사용하는 헤더들 \
    [X-amz-date](https://docs.aws.amazon.com/ko_kr/IAM/latest/UserGuide/reference_sigv-signing-elements.html#date) \
    [x-amz-content-sha256](https://docs.aws.amazon.com/ko_kr/IAM/latest/UserGuide/reference_sigv-create-signed-request.html) \
    [x-amz-server-side-encryption](https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/userguide/UsingServerSideEncryption.html)

- X-Forwarded-For: HTTP 프록시나 로드 밸런서를 통해 웹 서버에 접속하는 클라이언트의 원 IP 주소를 식별하는 사실상의 표준 헤더, 표준화된 버전은 HTTP [Forwarded](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/Forwarded) 헤더 근데 X-Forwarded-For가 더 표준에 가까움

- X-Real-Ip: 웹 서버에서 클라이언트의 실제 IP 주소를 식별하는 데 사용

    https://umbum.dev/1221/

- X-Frame-Options: 해당 페이지를 `<frame>` 또는`<iframe>`, `<object>`에서 렌더링할 수 있는지 여부를 나타내는데 사용 -> 이제는 CSP의 frame-ancestors가 대체함

- X-Content-Type-Options: Content-Type를 따라 MIME 유형을 의도적으로 지정 -> MIME sniffing을 방지

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