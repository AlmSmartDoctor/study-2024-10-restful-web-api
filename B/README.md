# 부록 B. 헤더 목록

## 목차

1. 헤더
2. 커스텀 헤더
3. 질문

## 헤더

HTTP 헤더: HTTP 요청이나 응답 프로토콜 의미 체계를 설명하는 메타데이터

Accept

Allow

Authorization

Cache-Control

Content-Encoding

Content-Type

Cookie

Etag

Host

Location

Set-Cookie

User-Agent

Access-Control-Allow-Origin

Content-Security-Policy

Strict-Transport-Security

## 커스텀 HTTP 헤더

새로운 HTTP 헤더를 만든다면 기존 HTTP 헤더처럼 문서를 만들어야 한다.

하이퍼미디어 데이터 형식들이 점점 많아지고 유연해지면서 커스텀 헤더에 들어가던 정보들이 표현에 직접 들어갈 수 있게 되었다.
기존 믿어 유형에 새로운 애플리케이션 의미 체계를 추가하려 한다면, 새로운 HTTP 헤더 대신에 프로파일로 정보를 넣을 수 있다.

원래 확장(extension)을 의미하는 X-로 커스텀 헤더 이름을 시작했는데, RFC 6648에서는 그냥 X- 빼도록 설명되어있다.
=> 만약 커스텀 헤더가 표준화되었을 때 X-를 사용하던 기존 클라이언트에 영향을 줄 것이다

X-amz-ad, X-amz-date, x-amz-content-sha256, x-amz-server-side-encryption

X-Forwarded-For

X-Real-Ip

X-Frame-Options

X-Content-Type-Options

## 3. 질문