# 4장. 하이퍼미디어
> 발표일 `24.11.14`
>
> 발표자 `임해찬`

## 목차
1. 하이퍼미디어
2. 하이퍼미디어 컨트롤의 세 가지 역할
3. 질문
---
## 1. 하이퍼미디어
> 서버가 클라이언트에게 나중에 어떤 HTTP 요청을 보낼 수 있을지 설명하는 방식.

( = 한 페이지에서 이동할 수 있는 페이지들의 모음 )

---
## 2. 하이퍼미디어 컨트롤의 세 가지 역할
1. `HTTP 요청 형식`을 결정 (메서드, URL, 헤더, 바디 등)
	- ex. \<a\> 태그 -  URL: href 속성으로 주어진 값, 메서드: GET.
	- ex 2. HTML 폼 - URL: action 속성으로 주어진 값, 메서드: method 속성으로 주어진 값, Content-Type: application/x-www-form-urlencoded 등.
2. `HTTP 응답 형식`을 결정 (상태 코드, 헤더, 바디 등)
    - ex. \<img\> 태그 - 이미지 표현의 한 종류
    - ex2. \<link rel="edit /> - 리소스가 GET, PUT, DELETE도 지원함, Content-Type: application/atom+xml
3. `클라이언트의 응답 처리 방식`을 제시
    - ex. \<img\> 태그 - 별도의 HTTP 요청을 통해 자동으로 로드뒈어 웹 페이지에 표시.
---
## \+ 하이퍼미디어 올바르게 사용하기
- URL 링크를 직접 보내주는 등의 행위 금지 (이러면 문서화가 필요해진다)
---
## \+ URL vs URI
- URI, URL 모두 리소스를 식별하는 문자열.
- URL: Uniform Resource Locator - 리소스의 위치를 찾아갈 수 있음
- URI: Uniform Resource Identifier - 리소스의 위치를 찾아갈 수 없을 수도 있음. Ex. ISBN
---
## 3. 질문
## (박근영) 질문 1
> 하이퍼미디어 표준들에 대해 소개해주세요

- [JSON HAL](https://en.wikipedia.org/wiki/Hypertext_Application_Language): Hypertext Application Language. 하이퍼미디어를 JSON or XML로 표현하는 방법을 정의.
- [RFC 5988](https://datatracker.ietf.org/doc/html/rfc5988): Web의 자원간의 관계를 표현하는 link에 대한 기준 제시.

## (최준영) 질문 2
> 찾아보니 HATEOAS api 응답에서 links 필드 타입을 object로 쓰는 예시도 있고 array로 쓰는 예시도 있던데 object가 클라이언트 단에서 개발할 때 더 유리해 보입니다 혹시 array일 때 장점이 있거나 두가지 타입의 용도가 따로 특정되어 있나요?

- links 프로퍼티가 object 형태로 구성.
- href 프라퍼티: IRI. 일반적으로 URL.
- rel 프라퍼티(option): target 리소스와의 관계
- type 프라퍼티(option): target 리소스와의 미디어타입.

## (한태혁) 질문 3
> script 태그는 자바스크립트 코드를 다운로드해서 실행시키고
link rel="stylesheet"는 스타일 시트를 다운로드해서 문서에 적용하고
img 태그는 자동으로 HTTP 요청을 통해 이미지를 불러오는데
어떻게 이런 기능들이 작동되고, 또 어떤 순서로 동작이 이루어지는 궁금합니다

- script 태그 -> blocking. 코드를 모두 실행할 떄까지 파싱 중단.
- link 태그 (rel="stylesheet") -> 비동기. 하지만 필요에 따라 렌더링 지연.
- img 태그 -> 비동기. 비차단. ([출처](https://velog.io/@chltjdrhd777/CSS-의-fetching은-Asynchronous하지만-Blocking스럽다))

## (서형석) 질문 4
> 하이퍼미디어 컨트롤이 어떻게 의미적 차이를 이어줄 수 있는지 설명해주세요
