# 12장 리소스 설명과 연결된 데이터 (~연결된 데이터 운동)

## 목차

1. RDF
2. 설명 전략을 사용해야 할 때
3. 리소스 유형
4. RDF 스키마
5. 연결된 데이터 운동

표현 전략

> 클라이언트가 GET 요청을 리소스의 URL로 보내고 해당 리소스의 표현을 받는 것

설명 전략

> 다른 리소스에 대해 설명

```json
{
  "collection": {
    "version": "1.0",
    "href": "http://www.youtypeitwepostit.com/api/",
    "items": [
      {
        "href": "/api/messages/21818525390699506",
        "data": [{ "name": "text", "value": "Test." }]
      },
      {
        "href": "/api/messages/3689331521745771",
        "data": [{ "name": "text", "value": "Hello." }]
      }
    ]
  }
}
```

설명 전략에만 집중한 형식 그룹 존재

## 1. RDF

> RDF (Resource Description Framework)는 웹 상의 리소스를 기술하기 위한 W3C 표준

기본 구조: 주어-서술어-목적어 (triple)

- 주어: URI 또는 블랭크 노드
- 서술어: URI
- 목적어: URI, 리터럴 또는 블랭크 노드

[RDF+XML]

```xml
<rdf:RDF xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
         xmlns:maze="http://alps.io/example/maze#">
  <rdf:Description rdf:about="http://example.com/cells/M">
    <maze:title>The Entrance Hallway</maze:title>
    <maze:east resource="http://example.com/cells/N">
    <maze:west resource="http://example.com/cells/L">
  </rdf:Description>
</rdf:RDF>
```

[Turtle]

```ttl
<http://example.com/cells/M> <http://alps.io/example/maze#title> "The Entrance Hallway" .
<http://example.com/cells/M> <http://alps.io/example/maze#east> <http://example.com/cells/N> .
<http://example.com/cells/M> <http://alps.io/example/maze#west> <http://example.com/cells/L> .
```

[Turtle 단축]

```ttl
@prefix maze: <http://alps.io/example/maze#> .
<http://example.com/cells/M> maze:title "The Entrance Hallway" ;
                             maze:east <http://example.com/cells/N> ;
                             maze:west <http://example.com/cells/L> .
```

RDF는 URL을 URI로 취급

- URI: 자원 식별자
- URL: 자원 식별자 + 위치

연결 반대편에 무엇이 있는지 확인하기 위해 연결을 따라가는 것이 아니라 판단해야 함

## 2. 설명 전략을 사용해야 할 때

1. 내가 표현을 제어하지 않는 리소스에 대해 이야기하는 경우
2. 하이퍼미디어 제어를 포함하지 않는 표현을 제공하는 API 문서에 하이퍼미디어를 추가하는 경우
3. 웹에 표현이 없는 리소스에 대해 이야기하는 경우

```ttl
@prefix schema: <http://schema.org/> .
<urn:isbn:9781449358063> a schema:Book ;
schema:name "RESTful Web APIs" ;
schema:inLanguage "en" ;
schema:isbn "9781449358063" ;
schema:author _:mike ;
schema:author _:leonard .

_:mike a schema:Person ;
schema:name "Mike Amundsen" .

_:leonard a schema:Person ;
schema:name "Leonard Richardson" .
```

## 3. 리소스 유형

시멘틱 웹의 가장 유용한 아이디어 중 하나는 리소스가 하나 또는 하나 이상의 리소스 유형으로 분류될 수 있다는 것이다.

RDF의 type 속성은 유형을 리소스에 할당한다.

```ttl
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
<http://example.com/~omjennyg> <rdf:type> <http://schema.org/Person> ;
<http://schema.org/birthDate> "1987-08-25" .
```

```ttl
<http://example.com/~omjennyg> a <http://schema.org/Person> ;
<http://schema.org/birthDate> "1987-08-25" .
```

XLink 형식에서 연결은 role 속성을 가질 수 있는데, 이는 연결의 반대 끝에 있는 리소스의 유형을 알려준다.

```xml
<a xlink:href="http://example.com/~omjennyg"
   xlink:arcrole="http://alps.io/iana/relations#author"
   xlink:role="http://schema.org/Person">
```

CoRE 연결 형식에서 연결의 rt 속성은 반대 끝에 있는 리소스의 유형을 전달한다.

```CoRE
<http://example.com/~omjennyg>;rel="author";rt="http://schema.org/Person"
```

'리소스 유형'의 개념은 RDF 으로부터.

## 4. RDF 스키마

> 기본 RDF 어휘의 확장

https://www.w3.org/TR/rdf-schema/#ch_type

```rdf
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
<http://schema.org/Person> a rdfs:Class ;
rdfs:label "Person" ;
rdfs:comment "A person (alive, dead, undead, or fictional)." ;
rdfs:subClassOf <http://schema.org/Thing> .
```

RDF 스키마 프로파일은 더 많은 API 애플리케이션 의미 체계를 기계가 이해할 수 있는 형태로 넣는다.

## 5. 연결된 데이터 운동

RDF는 URI를 사용함

RDF만으로는 RESTful API를 만들 수 없음

1. 사물을 위한 이름으로 URI를 사용한다.

2. HTTP URI를 사용하여 사람들이 이 이름으로 찾도록 한다.

   - urn:isbn:9781449358063 (x) / http://example.com/books/9781449358063 (o)
   - 리소스는 표현을 가져야만 함

3. 누군가가 URI에 접근할 때, 표준을 사용하여 유용한 정보를 제공하도록 한다.

4. 다른 URI로 연결을 포함하여 사람들이 더 많은 것을 찾아볼 수 있도록 한다.

## 질문

### Q1.

> 이창준
>
> URL 과 URI 의 차이점이 뭐고 이 차이점이 왜 RDF를 RESTful API 에 바로 적용할 수 없는 이유인지 간단하게 설명 부탁드립니다. p.329

RDF는 URL 형식이더라도 URI로 인식하기 때문에, 많은 필딩 제약을 무시함

### Q2.

> 안재우
>
> 한 리소스의 rdfs subClassOf, subPropertyOf, domain, range와 같은 속성들이 여러개의 값을 가질 수도 있나요? 그렇다면 스키마 문서상으로 어떻게 표현되나요?

https://stackoverflow.com/questions/39505700/rdf-more-than-one-object-for-one-subject-property

https://groups.google.com/g/json-ld/c/ZdBWem5OBW8

### Q3.

> 정예찬
>
> RDF Turtle 의 다른 축약어나 기능에 대해 설명해주세요

https://www.w3.org/TR/turtle/#object-lists

### Q4.

> 박지환
>
> (p.324) "설명 전략 대신에 표현 전략을 사용하기로 결정하면 아마도 RDF/XML이나 RDF/Turtle보다 하이퍼미디어 제어에 더 나은 다른 데이터 형식을 사용하길 원할 것이다."
> 여기서 언급한 '더 나은 데이터 형식'의 예시와 더 나은 이유를 알려주시면 감사하겠습니다!

Collection+JSON

### Q5.

> 정시우
>
> OWL은 RDF와 어떤 차이점이 있나요? RDF의 어떤 단점을 해결했는 지, 어떻게 사용하는 지 알려주세요.

OWL(Web Ontology Language)

OWL은 RDFS의 확장

```xml
<rdf:Description rdf:about="#hasParent">
  <owl:inverseOf rdf:resource="#hasChild"/>
</rdf:Description>
```

owl:equivalentClass
owl:intersectionOf
owl:unionOf

### Q6.

> 공병규
>
> schema.org가 어느정도의 공신력이 있는 단체인지, schema.org를 실제로 사용하는 예시 등이 궁금합니다

https://schema.org/docs/about.html

https://blog.naver.com/naver_search/223267245336
