# 08-2
<br /><br /><br /><br /><br /><br />
## XMDP
- 메타데이터 프로파일
- hCard
- <a href="https://gmpg.org/xmdp/1">https://gmpg.org/xmdp/1</a>
<br />
- <a href="https://en.wikipedia.org/wiki/XMDP">https://en.wikipedia.org/wiki/XMDP</a>
<br />

![image](https://github.com/user-attachments/assets/755a1945-678a-44f3-84cc-036b80aad697)
<br />
![image](https://github.com/user-attachments/assets/0109edf1-dabd-4ccd-92b8-40541c639fc4)

<br />
<br /><br />
- <a href="https://en.wikipedia.org/wiki/HCard">https://en.wikipedia.org/wiki/HCard</a>
<br /><br /><br />
HTML
<br />

![image](https://github.com/user-attachments/assets/5d49b2eb-4fa1-47d6-896f-f6dd136a9050)
<br />
마이크로포맷 마크업
<br />

![image](https://github.com/user-attachments/assets/1965feb6-b6c8-40cb-9e70-ee82de81414a)
<br />
프로파일을 헤더에 추가할 수 있음
<br />

![image](https://github.com/user-attachments/assets/c2a1ac74-ed61-4de5-b033-190cc52d91e7)
<br />

<br /><br /><br /><br /><br /><br />

## ALPS
![image](https://avatars0.githubusercontent.com/u/3815757?v=3&s=200)
- HTML 마이크로포맷을 설명하기 위한 HTML 마이크로포맷 (초기, 책 내용)
- JSON이나 XML도 가능 (언어 독립적 특성)
  <a href="https://datatracker.ietf.org/doc/html/draft-amundsen-richardson-foster-alps-06">alps</a>
![image](https://github.com/user-attachments/assets/2bf7f8d2-c1a7-40fc-b7eb-7f60a501b9d8)
<br /><br /><br /><br /><br /><br />


##JSON-LD
-<a href="https://json-ld.org/">https://json-ld.org/</a>
<br />
-![image](https://github.com/user-attachments/assets/985c4c2a-a8e4-4255-b4bb-ef056db22cb1)
<br />
- JSON 기반
- 링크드 데이터 (JSON-LD 콘텍스트에 연결하는 연결을 제공)
- @context - 데이터 정의 제공
- "@context": "https://schema.org" -  https://schema.org 의 표준에 따라 해석
- @id - 고유 식별자 정의
- @type - 리소스의 유형 정의
- 속성 - 리소스의 속성과 값을 표현
<code>
{
  "@context": {
    "name": "http://schema.org/name",
    "description": "http://schema.org/description",
    "image": {
      "@id": "http://schema.org/image",
      "@type": "@id"
    },
    "geo": "http://schema.org/geo",
    "latitude": {
      "@id": "http://schema.org/latitude",
      "@type": "xsd:float"
    },
    "longitude": {
      "@id": "http://schema.org/longitude",
      "@type": "xsd:float"
    },
    "xsd": "http://www.w3.org/2001/XMLSchema#"
  },
  "name": "The Empire State Building",
  "description": "The Empire State Building is a 102-story landmark in New York City.",
  "image": "http://www.civil.usherbrooke.ca/cours/gci215a/empire-state-building.jpg",
  "geo": {
    "latitude": "40.75",
    "longitude": "73.98"
  }
}
</code>

<br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br />


# 질문


> 안재우 <br />
> 책에서 JSON-LD를 소개할 때 예시에는 @context, @id, @type의 속성들만 등장하지만 @vocab 등 더 다양한 속성들이 많은거로 알고 있습니다.
어떤 다른 속성들이 존재하는지, 그것들이 어떤 의미를 가지고 있는지 궁금합니다.
<br />
<a href="https://www.w3.org/TR/json-ld/#specifying-the-type">3.5 Specifying the Type</a>
<br />
<a href="https://www.w3.org/TR/json-ld/#term-selection">5.2.8 Term Selection</a>
<br />


<br /><br /><br /><br /><br />

> 이창준 <br />
> 보통 구글과 같은 서치 엔진 로봇이 JSON-LD 형식을 선호하는거 같은데, 책의 내용과 더불어 해당 형식이 가져다 주는 이점에 대하여 설명해주세요
<br />

1. 기존 클라이언트들을 망가뜨리지 않고 JSON API 에 간단한 하이퍼미디어 컨트롤을 추가할 수 있다.
script 태그 안에 담겨오기 때문에 HTML 코드에 직접 속성을 추가하지 않아도 됨
<img src="https://github.com/user-attachments/assets/49fb6f4b-538c-4bef-9fda-c4f512311629" width="300px" />

3. SEO 이점 <a href="https://developers.google.com/search/docs/appearance/structured-data/product?hl=ko">구조화된 제품 데이터</a>
   리치 스니펫

4. 가독성, 유연성

<br /><br /><br /><br /><br />

> 박지환 <br />
>p.177에서 언급된 XSLT란 무엇인지, 그리고 '사람이 이해할 수 있는 문서로 변환' 외에 어떤 경우에 사용되는지 궁금합니다.

wiki: XSLT(Extensible Stylesheet Language Transformations)는 XML 문서를 다른 XML 문서로 변환하는데 사용하는 XML 기반 언어이다. W3C에서 제정한 표준으로 XML 변환 언어를 사용하여 XML 문서로 바꿔주며, 탐색하기 위해 XPath를 사용한다.
<br />
![image](https://github.com/user-attachments/assets/5dc31d9a-8e00-4fbc-9af2-d01ddb646c63)
<br />
wiki: "XSLT의 주요 목적은 XML을 처리하여 사람이 화면, 웹, 인쇄물로 읽을 수 있게 하는 것이다."
데이터 변환, 통합, 필터링 등 작업이 가능할 거 같습니다

<br /><br /><br /><br /><br />

> 공병규 <br/ >
>json-ld 예시에서 @type과 @id가 등장하는데, 두 필드의 차이점이 무엇인지 설명해주세요


@type은 해당 리소스가 어떤 클래스(class)에 속하는지 정의
	•예를 들어, 사람이면 Person, 장소면 Place와 같이 리소스의 본질적인 유형을 나타냄
 
@id는 특정 리소스를 식별하기 위한 고유 식별자(URI 또는 URL)를 제공함
	•이는 해당 리소스의 고유한 주소나 위치를 나타내며, 다른 데이터 세트나 시스템에서 이 리소스를 참조할 수 있도록 만듬

 
<br /><br /><br /><br /><br />

> 정시우 <br />
> 책에서 소개하지 않은 ALPS의 다른 기능들이 궁금합니다. 대표적인 기능들을 소개해주세요~

<a href="https://www.infoq.com/articles/programming-semantic-profiles"/>Programming with Semantic Profiles: In the Land of Magic Strings, the Profile-Aware is King - InfoQ</a>
