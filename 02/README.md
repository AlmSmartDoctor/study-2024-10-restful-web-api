# 2장. 간단한 API

### 2-0. 요약
- HTTP에 대한 간략한 소개 - 요청/응답, 메서드, Collection+JSON
- RESTful 디자인의 목표와 한계 - 제약을 통한 호환성 향상. 의미적 차이를 없앨 순 없다.
---
### 2-1. HTTP 요청/응답, 메서드
- GET 요청: 표현을 요청하는 것. **안전한 메서드**. 서버의 리소스에 부수적인 변화는 있을 수 있지만, 서버의 리소스를 변경하기 위한 메서드는 아님.
- 응답: 상태 코드 + 엔티티 바디 + 응답 헤더
  - Content-Type: 미디어 유형(또는 MIME 유형)이라고 부른다. HTTP 클라이언트에게 엔티티 바디의 형식을 알려준다. ex. text/html, image/jpeg 등.
---
### 2-2. Collection+JSON
- 웹에서 검색 가능한 리소스 목록을 발표하는 표준.
- href, items, templete 등의 프라퍼티를 갖는다.
---
### 2-3. RESTful의 목표와 한계
- 제약을 통한 자유. ex. HTTP GET 메서드는 안전한 메서드. 응답에 숫자 9 -> 숫자인지 문자인지 등.
- 거의 비슷한 일을 거의 비슷하게 할 수 있다.
- 의미적 차이는 극복할 수 없다. ex. 마이크로블로그 - text, 요리책 - ingredients.
---
### (곽재영) 질문 1. 
```
책(p.26)에서 application/vnd.collection+json은 Collection+JSON에 등록된 미디어 유형이라고 합니다.
그렇다면 이것이 가장 많이 사용되는 json 미디어 유형인가요? 또 다른 유명한 json 미디어 유형도 있나요?
```
- 검색해본 결과 나오는 내용이 거의 없는 걸로 봐서는 일반적으로 사용되는 형식은 아닌 듯 합니다.

- 많이 사용되는 미디어 유형 (출처 : [웹 개발자들을 위한 중요한 MIME 타입](https://developer.mozilla.org/ko/docs/Web/HTTP/MIME_types#웹_개발자들을_위한_중요한_mime_타입))
    - application/octet-stream(이진 파일을 위한 기본 값)
    - text/plain(텍스트 파일을 위한 기본 값), text/css, text/html, text/javascript
    - image/jpeg, image/gif
    - audio/webm, audio/wav
    - multipart/form-data(서버로 form data를 보낸다)
---
### (한태혁) 질문 2. 
```
22p에서 wget을 사용하여 GET 요청을 보내는데, 책에서 소개된 옵션 이외에 유용한 옵션은 어떤 게 있나요?
+ GET이 아닌 POST 요청은 어떻게 보낼 수 있나요?
```
- 유용한 옵션: 파일 다운로드 시 다른 이름으로 저장, 속도 제한, 백그라운드에서 다운로드, 특정 타입 제외 등
- Wget post 요청 보내기
    - wget -- post-data="key=value" http://...
- 여러 가지 커맨드라인 도구들: Wget, cURL, resty, httpie 등
    - 응답 헤더 출력, 요청 헤더 지정, 요청 본문 지정 등
---
### (서형석) 질문 3. 
```
책에서는 1. 마이크로블로깅 api 설계 + 2. application/vnd.collection+json content-type의 조합으로 간단한 api 설계 예시를 설명하는데요,

마이크로블로깅 api + 다른 content-type, 혹은
다른 목적의 api + application/vnd.collection+json content-type의 예시에는 어떤 것들이 있을까요?
```
- 애초에 Collection+JSON 미디어 타입이 많이 사용되는 형식이 아닌 것 같습니다. 예시가 있긴 하지만 설명을 위한 예시입니다. [Collection+JSON - Examples](http://amundsen.com/media-types/collection/examples/))
- Collection+JSON 형식 자체가 REST API를 전제로 하는 형식인 것 같습니다.
---
### (정시우) 질문 4. 
```
JSON 데이터 형식은 기본적으로 텍스트 기반의 정보 교환에 많이 사용되는데,멀티미디어(이미지, 음성, 영상 등)를 처리하기 위한 표준에는 어떤 것이 있고, 어떤 제약을 사용했나요?
```
- 멀티미디어를 위한 데이터 표기법이 따로 있는 것 같지는 않습니다. 결국은 binary 코드로 변환하고 이를 직접 전송하거나 인코딩(Base64 등)하여 전송하는 방법을 사용하는 것 같습니다.
