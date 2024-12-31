# 11장. API를 위한 HTTP (~ 인증)
> 발표일 `24.12.31`
>
> 발표자 `서형석`

## 목차

1. 개요
2. 표현들 사이에서 선택하기
3. HTTP 성능
4. 질문

## 개요

![image](https://github.com/user-attachments/assets/e48ccccc-423b-4dcb-b9f7-aa8856277af9)


- 월드 와이드 웹을 기술 스택으로 생각
    - 하이퍼미디어: 하나의 웹 사이트나 API의 프로토콜 의미 체계를 설명, "다음은 무엇인가?"
    - HTTP 프로토콜: 리소스 위에서 리소스 표현을 읽는 권한과 기본적인 리소스 상태에 쓰는 권한을 제공, "내가 어떻게 이 리소스와 통신을 하는가?"에 대한 답
    - URL: 리소스, "리소스는 어디에 있나?"에 대한 답

- 하이퍼미디어는 쉬고, HTTP의 심화된 프로토콜 의미 체계를 설명


### 응답코드
- RFC 2616 - 41가지 HTTP 응답 코드 정의
### 헤더
- RFC 2616 - 47가지 HTTP 요청과 응답 헤더 정의

## 표현들 사이에서 선택하기

- 단일 리소스는 많은 표현을 가질 수 있다 (XML과 JSON 제공)
- 리소스는 '개요 표현(overview representation)'과 '세부 표현(detail representation)'을 가질 수 있다
- 서버가 하나의 리소스에 대해 다양한 표현을 제공할때 클라이언트가 어떻게 구분할까? 두 가지 방법이 있다.


### 콘텐트 협상
- 클라이언트가 어떠한 표현을 원하는지 서버에 알리기 위해 특별한 HTTP 요청 헤더를 사용할 수 있음

#### Accept

```
웹 API 클라이언트
Accept: application/vnd.collection+json

웹 브라우저
Accept: text/html, application/xhtml+xml, application/xml; q=0.9, */*; q=0.8
```
- HTTP 표현들과 XHTML 표현들을 최우선으로 두고, XML 표현은 우선순위가 낮다. 
- HTML이나 XML 모두 서버가 제공할 수 없다면 모든 미디어 유형(*/*)을 수락한다.

#### Accept-Language
```
Accept-Language: en-us, en; q=0.5
```
- 미국 영어를 선호하지만 두 번째 선택으로 다른 모든 종류의 영어를 수락

### 하이퍼미디어 메뉴

- 클라이언트가 원하는 표현을 일반적으로 어떻게 찾을까?
    - 클라이언트는 API의 URL로 GET요청을 보내고, 서버는 다른 리소스들로의 하이퍼미디어 연결을 포함한 홈페이지 표현을 제공
        - 클라이언트는 따라가길 원하는 연결을 선택하고 다른 GET 요청을 만듦
        - '리소스 협상'과정은 없음
- 서버는 응답 코드 300(Multiple Choices)를 보내서 리소스들 사이 선택을 제안
    - 이 경우 다른 연결 끝에 무엇이 있는지를 설명하는 하이퍼미디어 유형을 사용해야 함
    - html a와 link 태그가 type 속성을 통해 이를 지원

```
<a href = "/resource/siren" type = "application/vnd.siren+json" rel="alternate">
  The Siren version.
</a>

<a href = "/resource.es" hreflang="es">
  Para la version ne espanol, haga clic aqui.
</a>
```

- 대부분의 하이퍼미디어 유형이 이러한 기능들을 가지고 있지 않으므로 헤더 기반 콘텐츠 협상을 보통 추천

### 정규 URL (Canonical URL)

- 리소스가 하나 이상의 URL을 가지고 있을 때면 이것들 중 하나를 정규 URL로 결정해야 함.
    - 표준 HTTP 헤더 Content-Location을 현재 리소스의 정규 URL을 가리키는 하이퍼미디어 컨트롤로 사용
    - IANA에 등록된 연결 관계인 canonical이 있음.

## HTTP 성능

- 몇 가지 시간을 낭비하는 요청을 위해 여러 가지 최적화를 정의
    - 의미 없는 요청들을 감소시킴 (캐싱)
    - 의미 없는 요청의 비용르 감소시킴 (조건적 요청)
    - 전반적으로 요청의 비용을 감소시킴 (압축)

---

### 캐싱

- RFC 2616은 캐시 무효화에 대한 자세한 규칙과 캐싱 프락시 같은 HTTP 중개자에 연관된 많은 이슈도 담고 있음.
- 여기서는 HTTP 헤더 Cache-Control을 사용해 웹 API에 캐시를 추가하는 가장 간단한 방법에 초점을 맞춤

```
HTTP/1.1 200 OK
Content-Type: text/html
Cache-Control: max-age=3600
```
- max-age 값은 클라이언트가 이 HTTP요청을 다시 만들기 전까지 얼마나 기다려야 하는지를 알려줌
- 30분 뒤에 요청을 보내길 원한다면, 한 시간 뒤까지 기다려야 함
- 클라이언트가 HTTP 응답을 보고 싶다면 요청을 다시 만들지 않고 캐시된 응답을 확인해야 함

```
HTTP/1.1 200 OK
Content-Type: text/html
Cache-Control: no-cache
```
- 클라이언트가 캐시를 한다고 하더라도 서버가 클라이언트에게 응답을 캐시하지 말라고 말함
- 리소스 상태가 휘발성이라 리소스의 표현이 결과를 보내는 동안에 아마도 쓸모없는 상태가 됨을 가리킴

Cache-Control을 설정하는 것은 얼마나 표현이 자주 변경될 것인지에 대한 판단을 필요로 함

---

### 조건적 GET(Conditional GET)

- 언제 리소스 상태가 변경되는지 모르는 경우가 있음
    - 컬렉션 유형 리소스들. 항상 변화할 수도 있고 얼마나 자주 변화가 일어나는지 예측하지 못할 수도 있음
- 클라이언트가 해당 요청이 필요할 때마다 요청을 만들도록 하고 변경된 사항이 없으면 서버의 요청을 제거하는 방법이 있음 -> 조건적 GET

#### 1. Last-Modified
- 언제 이 리소스의 상태가 마지막으로 변경되었는지 클라이언트에게 알려줌
```
HTTP/1.1 200 OK
Content-Length: 41123
Content-type: text/html
Last-Modified: Mon, 21 Jan 2013 09:35:19 GMT

<html>
...
```

클라이언트는 Last-Modified 값을 기록하고 다음번 요청을 만들떄 If-Modified-Since에 넣음
```
GET /some-resource HTTP/1.1
If-Modified-Since: Mon, 21 Jan 2013 09:35:19 GMT
```

- If-Modified-Since에 주어진 날짜 이후로 리소스가 변경되었다면 응답코드 200, 업데이트된 Last-Modified 값과 전체 표현을 보내줌

```
HTTP/1.1 200 OK
Content-Length: 44181
Content-type: text/html
Last-Modified: Mon, 27 Jan 2013 07:57:10 GMT

<html>
...
```

- 표현이 마지막 요청 이후 변경되지 않았다면 상태 코드 304(Not Modified)를 엔티티 바디 없이 보냄
```
HTTP/1.1 304 Not Modified
COntent-Length: 0
Last-Modified: Mon, 27 Jan 2013 07:57:10 GMT
```

서버와 클라이언트 양쪽 모두의 시간과 네트워크 사용량을 절약하나, 마지막으로 변경된 시점을 계속 추적해야 한다는 부가적인 작업을 해야한다.

#### 2. ETag - 더 쉽게 구현할 수 있는 또다른 전략
요청에 대응하는 표현이 변할 때마다 변경되어야 하는 무의미한 문자열을 포함
```
HTTP/1.1 200 OK
Content-Length: 44181
Content-type: text/html
ETag: "7359b7-a37c-45b333d7"
```
클라이언트가 같은 리소스에 대해 두 번째 요청을 할 때 If-None-Match 헤더를 원래 응답의 ETag에서 가져온 값으로 설정
```
GET /some-resource HTTP/1.1
If-None-Match: "7359b7-a37c-45b333d7"
```
- If-None-Match에 있는 ETag가 표현의 현재 ETag와 같으면 서버는 304 응답을 비어있는 엔티티 바디와 함께 돌려줌
- 표현이 변경되었다면 서버는 200응답을 전체 엔티티 바디, 갱신된 ETag와 함께 돌려줌

Last-Modified나 ETag 모두 조건적 요청을 지원. 두 가지 모두 지원하는 것이 이상적이고 ETag가 Last-Modified보다 더 신뢰할 만함.

----

### LBYL(Look-Before-You-Leap) 요청

조건적 GET은 많은 수의 클라이언트가 이미 가진 표현을 또 요청하는걸 방지하기 위함

또 다른 기능으로 무의미하게 많은 표현을 서버로 보내는 것을 방지

```
PUT /filestore/myfile.txt HTTP/1.1
Host: example.com
Content-length: 524288000
Expect: 100-continue
```
- Expect 요청 헤더를 문자열 100-continue로 설정해서 보냄
- 실제 PUT 요청이 아니고, 미래에 사용 가능한 PUT 요청에 대한 질문.
`"내가 새로운 표현을 /fliestore/myfile.txt로 PUT하게 허가하는가?"`
    - `예`라면 서버는 상태 코드 100(Continue)를 보내고, 클라이언트는 Expect 헤더를 제거하고 500MB 크기 파일을 엔티티 바디에 포함한 PUT 요청을 다시 보낼 것으로 기대
    - `아니오`라면 서버는 상태 코드 417(Expectation Failed)을 보냄. 
- 클라이언트가 거절당할 500MB 크기의 데이터를 보내는 상황을 방지함

---

### 압축

```
GET /resource.html HTTP/1.1
Host: www.example.com
Accept-Encoding: gzip
```
- Accept-Encoding 헤더 : 어느 압축 알고리즘을 클라이언트가 이해할 수 있는지 알려줌. 
- IANA 페이지(http://www.iana.org/assignments/http-parameters/http-parameters.xml, 'content-codings' 목록)

```
HTTP/1.1 200 OK
Content-Type: text/html
Content-Encoding: gzip
```
- 서버는 표현이 압축되지 않았을 때와 동일한 Content-Type을 보내고, Content-Encoding 헤더를 보내 문서의 압축 여부를 알 수 있음

클라이언트 입장에서 보면 HTML을 요청해 다시 HTML을 받음. 낮은 비용의 복잡도로 네트워크 전송량을 많이 줄여줌.

---

### 부분 GET(Partial GET)
- 클라이언트가 표현의 일부분만 받을 수 있도록 허용. 보통 중단된 다운로드를 다시 이어받을 때 사용

```
HTTP/1.1 200 OK
Content-Length: 1271174395
Accept-Ranges: bytes
Content-Type: video/mpeg
```
- 부분 GET을 지원하는 리소스는 Accept-Ranges 응답 헤더에 문자열 bytes를 설정

다운로드가 중단되면 클라이언트는 중단된 지점부터 다운로드 재개 가능
```
GET /large-video-file
Range: 1271173371-
```

응답은 다음과 같음

```
206 Partial Content
Content-Type: video/mpeg
Content-Range: 1271173371-1271174395
Content-Length: 1024
```
---

### 파이프라이닝

- 파이프라이닝은 클라이언트가 여러 개의 HTTP 요청을 한 번에 보낼 수 있도록 허용함으로써 지연을 감소시킴.
- 클라이언트는 멱등성이 있는 연속된 HTTP 요청들을 연속된 요청 전체가 멱등성이 있는 한 파이프라이닝 할 수 있다
```
GET /resource
DELETE /resource
```
- GET과 DELETE는 멱등성이 있지만, 조합은 그렇지 않음. 요청을 보내고 네트워크에 문제가 생겨 첫 번째 응답을 받지 못했다면, 요청을 다시 보냈을 때 같은 결과를 받을 수 없다.
    - 이러한 복잡성 때문에 GET 요쳥들의 문자열에 대해서만 파이프라이닝을 추천
- 성능 향상에 그다지 도움을 주지 않음
    - 클라이언트가 같은 도메인에 길고 연속된 HTTP 요청들을 보낼 때만 성과를 내는데, 웹 사이트들은 대부분 다른 도메인으로부터 온 요소들을 포함하고 있다.
- 브라우저가 아닌 API 클라이언트는 단일 도메인에 대해 길고 연속된 요청을 보내지만, 하이퍼미디어 기반 API에서도 그렇게 유용하지 않음.
    - 하이퍼미디어 API는 클라이언트가 다른 요청을 만들기 전에 이전 요청에 대한 응답을 분석해야 하기 때문.

---



## 질문

### Q1.
> 부분 GET 요청에서 클라이언트가 데이터를 어떻게 처리하는지 궁금합니다. 예를 들어 네트워크 상의 문제로 겹치는 구간이 중복으로 요청이 들어갔을 때, 중복으로 받은 데이터를 어떻게 처리하는지 등 p.297
>
> by 이창준

### A1. 

클라이언트는 일반적으로 이미 수신한 데이터의 상태를 추적하거나 고유 식별자를 사용해 중복 데이터를 제거.

네트워크 문제가 있는 상황에서도 데이터 일관성을 유지하려면 적절한 캐싱, 데이터 병합 로직, 또는 중복 검사 메커니즘을 구현해야 함

---

### Q2.

> 응답의 Cache-Control 값으로 API 응답에 대한 캐싱을 설정해 주던데 클라이언트가 해당 값이 존재해도 그것을 무시하고 캐시 수명이 다하기 전에 api 요청을 재전송할 수 있는 방법이 존재하나요?
>
> by 안재우

### A2.

캐시 수명이 남아 있어도 클라이언트는 다음과 같은 방법으로 캐시를 무시하고 API 요청을 재전송할 수 있음.

1. 요청 헤더에 Cache-Control: no-cache 추가.
2. URL에 쿼리 매개변수 추가.
3. 강제 새로고침 또는 캐시 비활성화.
4. 서버 정책 또는 클라이언트 설정을 활용.

이러한 방법을 조합하면 클라이언트가 원하는 시점에 서버와 동기화할 수 있음.

---

### Q3.

> p.288에서 언급된 콘텐트 협상의 5가지 요청 헤더 중 Accept와 Accept-Language를 제외한 나머지 3개의 요청 헤더도 소개해주시고, 각각 예시를 들어주시길 바랍니다!
>
> by 박지환

### A3.

#### 1. Accept-Charset

설명: 클라이언트가 서버에 요청한 콘텐츠에서 사용 가능한 문자 인코딩(charset)을 명시합니다.

예시:
```
GET /example HTTP/1.1
Host: www.example.com
Accept-Charset: utf-8, iso-8859-1;q=0.7
```

해석:
- 클라이언트는 UTF-8 인코딩을 선호하며, ISO-8859-1을 우선순위 0.7로 지원합니다.

서버 응답:
- UTF-8로 인코딩된 콘텐츠를 반환하거나, 지원하지 않는 경우 406 Not Acceptable을 반환.

#### 2. Accept-Encoding

설명: 클라이언트가 서버에 요청한 콘텐츠에서 지원 가능한 압축 방식(encoding)을 명시합니다.

예시:
```
GET /example HTTP/1.1
Host: www.example.com
Accept-Encoding: gzip, deflate, br
```

해석:
- 클라이언트는 gzip, deflate, brotli 압축 방식을 지원합니다.
서버 응답:

- 서버는 요청된 파일을 gzip으로 압축한 후 전달할 수 있습니다:
```
HTTP/1.1 200 OK
Content-Encoding: gzip
```

#### 3. User-Agent

설명: 요청을 보낸 클라이언트 애플리케이션(브라우저, 앱, 기타)의 정보를 전달합니다.

예시:
```
GET /example HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/117.0.0.0 Safari/537.36
```

해석:
- 클라이언트는 Windows 10 OS에서 Chrome 브라우저를 사용하고 있습니다.

서버 응답:
- 서버는 User-Agent 정보를 사용하여 적합한 콘텐츠를 제공합니다. 예를 들어:
    - 데스크톱 페이지 또는 모바일 페이지를 선택.
    - 브라우저 호환성에 맞는 CSS/JavaScript 제공.

---

### 04.

> 부분 GET 요청에서 받으려는 데이터가 새로 업데이트 된 경우에는 클라이언트에서 어떻게 이를 감지하고 새로 요청받을 수 있을까요?
>
> by 공병규

### A4,

일반적으로 조건부 요청(Conditional Request) 또는 폴링 및 푸시 기반의 메커니즘을 통해 이루어짐

| 방법	| 특징	| 적합한 사용 사례 |
| --- | ---- | ------------ |
| 조건부 요청 |	ETag/Last-Modified로 변경 확인.	| 데이터 변경 빈도가 낮을 때 사용. |
| 정기 폴링	 | 간격을 정해 반복적으로 요청.  |	변경 주기를 예측하기 어려운 경우. |
| 장기 폴링	| 새 데이터가 있을 때 응답 반환. |	실시간 데이터가 필요한 경우. |
| WebSocket/SSE	| 실시간 알림.	| 즉각적인 데이터 업데이트가 필요한 경우. | 
| 변경 여부 확인 API	| 변경 여부만 확인 후 필요한 경우 요청. |	데이터 변경이 드문 경우. |

---

### 05.

> 정규 URL의 canonical 연결 관계에 대해 설명해주세요.
>
> 정예찬

### A5.

 웹페이지의 여러 URL 버전 중 하나를 대표 URL로 지정하여 검색 엔진이 중복 콘텐츠를 올바르게 처리할 수 있도록 안내하는 방식. 이는 SEO(검색 엔진 최적화)와 중복 콘텐츠 문제 해결에 중요한 역할을 함

#### 1. Canonical의 필요성

(1) 중복 콘텐츠 문제

동일하거나 유사한 콘텐츠가 여러 URL로 접근 가능할 때, 검색 엔진은 이를 중복 콘텐츠로 간주하고 순위가 분산되거나 페널티를 받을 위험이 있습니다.

예를 들어, 아래의 URL들이 동일한 콘텐츠를 제공한다고 가정합니다:
- https://example.com/page
- https://example.com/page/
- https://example.com/Page
- https://example.com/page?ref=123

검색 엔진은 어떤 URL이 대표적인지 명확히 알지 못합니다.

(2) 캐노니컬 태그로 대표 URL 지정

캐노니컬 태그를 사용하여 특정 URL을 대표(Preferred) URL로 지정하면 검색 엔진이 이를 기준으로 콘텐츠를 처리하고, 순위를 통합합니다.


#### 2. Canonical Tag란?

HTML <link> 태그로 지정하며, 페이지의 <head> 섹션에 작성됩니다.

형식:
```
<link rel="canonical" href="https://example.com/page" />
```
이 태그는 "이 페이지의 대표 URL은 https://example.com/page"라는 것을 검색 엔진에 알려줍니다.

#### 3. Canonical 연결 관계의 원리

(1) 서버와 검색 엔진 간 협력

캐노니컬 태그를 통해 검색 엔진은 어떤 URL을 인덱싱의 기준 URL로 삼을지 결정합니다.
예를 들어, 동일한 콘텐츠를 제공하는 여러 URL이 있을 때:
- https://example.com/page
- https://example.com/page?ref=123
- https://example.com/page#section

이 중 https://example.com/page를 대표 URL로 설정하면 검색 엔진은 이를 기준으로 나머지 URL을 처리합니다.

(2) 순위 통합
중복된 URL들이 각각 개별적으로 순위 경쟁을 하지 않고, 캐노니컬 URL에 순위가 통합됩니다.
이는 트래픽 손실 방지와 SEO 점수 향상에 기여합니다.
