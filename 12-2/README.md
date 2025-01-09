# 12장 리소스 설명과 연결된 데이터 (JSON-LD~)

## 목차

1. JSON-LD
2. 히드라
3. XRD 종류들
4. 온톨로지 동물원
5. 결론: 설명 전략은 살아 있다!

## 1. JSON-LD

HTTP/1.1 200 OK </br>
content-Type: application/json

```json
{
  "n": "Jenny Gallegos",
  "photo_link": "http://api.example.com/img/omjennyg"
}
```

HTTP/1.1 200 OK </br>
content-Type: application/ld+json

```json
{
  "@context": {
    "n": "http://schema.org/name",
    "photo_link": {
      "@id": "http://schema.org/image",
      "@type": "@id"
    }
  }
}
```

HTTP/1.1 200 OK </br>
content-Type: application/ld+json

```json
{
  "n": "Jenny Gallegos",
  "photo_link": "http://api.example.com/img/omjennyg",
  "@type": "http://schema.org/Person",
  "@context": {
    "n": "http://schema.org/name",
    "photo_link": {
      "@id": "http://schema.org/image",
      "@type": "@id"
    }
  }
}
```

JSON-LD는 애플리케이션 의미 체계를 자세하게 표현할 수 있지만, GET 요청 외의 HTTP 요청 불가능 </br>
따라서 API에서 JSON-LD를 사용하길 원한다면 히드라 사용 추천

## 2. 히드라

히드라는 JSON-LD 콘텍스트로 JSON-LD에 많은 프로토콜 의미 체계를 추가한 JSON-LD의 파생 </br>
히드라를 추가하면 거의 모든 HTTP 요청을 지정할 수 있다.

http://purl.org/hydra/core/context.jsonld

## 3. XRD 종류들

XRD

> XML 기반 문서로, RDF와는 달리 <Property> 태그로 들어가는 의미 체계 서술자들과 <Link> 태그로 들어가는 연결 관계들을 구분한다.

```xml
<XRD xmlns="http://docs.oasis-open.org/ns/xri/xrd-1.0">
  <Subject>http://example.com/cells/M/</Subject>

  <Property type="http://alps.io/example/maze#title">
    The Entrance Hallway
  </Property>

  <Link rel="http://alps.io/example/maze#east" href="http://example.com/cells/N" />
  <Link rel="http://alps.io/example/maze#west" href="http://example.com/cells/L" />
</XRD>
```

JRD

```json
{
  "subject": "http://example.com/cells/M",
  "properties": {
    "http://alps.io/example/maze#title": "The Entrace Hallway"
  },
  "links": [
    {
      "rel": "http://alps.io/example/maze#east",
      "href": "http://example.com/cells/N"
    },
    {
      "rel": "http://alps.io/example/maze#west",
      "href": "http://example.com/cells/L"
    }
  ]
}
```

---

웹 호스트 메타데이터 문서

> 전체 API의 최상위 계층 설명을 포함한 XRD문서

XRD 연결은 href 대신 template 속성을 가질 수 있음

```xml
<Link rel="copyright" template="http://example.com/copyright?resource={uri}" />
```

---

웹핑거

> 웹핑거 프로토콜은 사용자 계정에 대한 정보를 찾아보는 데 필요한 JRD 문서 사용을 위한 이름

계정 식별: `acct:jenny@example.com`
계정 리소스 표현: `http://openid.example.com/users/omjennyg`

---

웹핑거 요청

GET /.well-known/webfinger?resource=acct%3Ajenny%40example.com </br>
HTTP/1.1

HTTP/1.1 200 OK </br>
Content-Type: application/jrd+json

```json
{
  "subject": "acct:jenny@example.com",
  "properties": {
    "http://schema.org/name": "Jenny Gallegos",
    "http://schema.org/email": "jenny@example.com"
  }
}
```

---

## 4. 온톨로지 동물원

### schema.org RDF

http://schema.org/docs/schema_org_rdfa.html

### FOAF

http://xmlns.com/foaf/spec/index.rdf

### vocab.org

http://vocab.org

## 5. 결론: 설명 전략은 살아 있다!

RDF 문서는 REST 관점에서 잘못된 것으로 보일 수 있다. </br>
하지만 RDF는 기계가 이해할 수 있는 애플리케이션 의미 체계 기술에 유리하고, 설명 전략이 유용한 경우가 있다. </br>
또한, 연결된 데이터 원칙을 통해 필딩 제약을 만족하면서 RDF 이점을 유지하는 것도 가능하다.

## 질문

### Q1.

> 곽재영
>
> JSON-LD를 API로 사용하는 실제 예시가 있는지 궁금합니다.

https://developers.google.com/knowledge-graph?hl=ko

### Q2.

> 한태혁
>
> JSON-LD 1.1은 JSON-LD 1.0과 비교해서 어떤 점들이 변경되었나요?

https://www.w3.org/TR/json-ld11

### Q3.

> 최준영
>
> SWEET 온톨로지에서 몇가지 소개해주세요

SWEET(Semantic Web for Earth and Environmental Terminology)

http://sweetontology.net/sweetAll
https://github.com/ESIPFed/sweet/blob/master/README.md

### Q4.

> 임해찬
>
> 연결된 데이터는 네 가지 원칙을 만족하는 데이터 모형이라고 하는데, JSON-LD가 어떻게 이 원칙들을 만족시키는지 설명해주시면 감사하겠습니다.

1. 사물을 위한 이름으로 URI를 사용한다.

2. HTTP URI를 사용하여 사람들이 이 이름으로 찾도록 한다.

3. 누군가가 URI에 접근할 때, 표준을 사용하여 유용한 정보를 제공하도록 한다.

4. 다른 URI로 연결을 포함하여 사람들이 더 많은 것을 찾아볼 수 있도록 한다.

```json
{
  "@context": "https://schema.org",
  "@id": "http://example.com/person/123",
  "@type": "Person",
  "name": "Alice",
  "knows": {
    "@id": "http://example.com/person/456",
    "@type": "Person",
    "name": "Bob"
  }
}
```

### Q5.

> 서형석
>
> 이번장의 온톨로지 동물원에 등장하는 하이퍼미디어 유형들은 실제로 쓰이나요?
> 10장에서 등장한 하이퍼미디어 동물원 유형들은 실제 자주 쓰인다기보단 기록을 위한 목적으로 기술된걸로 아는데,
> 그래프 데이터를 위한 미디어 유형들의 경우 그래프 데이터가 아닌 미디어 유형과 비교해 그들만의 사용례나 특수한 무언가가 또 있을까요?
> 초반부에 서술된 그래프 데이터의 특징(RDF, 연결된 데이터 운동)과 연관해 조사 부탁드려요.

SPARQL: RDF 질의어

https://searchadvisor.naver.com/guide/structured-data-intro
https://www.oss.kr/oss_case/show/a0468850-9800-484f-a8f7-155b1978bc13
