# 7장. 순수 하이퍼미디어 설계

## 목차

1. 왜 HTML인가?
2. 하이퍼미디어 컨트롤
3. 리소스 상태 변경하기
4. 질문


## 4. 질문

### Q1.
> 한태혁
> schema.org에서 schema.org vocabulary와 함께 사용할 수 있는 구조화된 데이터 형식 Microdata, RDFa, JSON-LD을 소개하는데
> 이 세 형식을 비교해 주세요

### A1.
| 특징 | Microdata | RDFa | JSON-LD |
|--------|--------|--------|--------|
| 표현 방식 | html 속성에 직접 삽입 | html/xml 속성으로 RDF 관계 표현 | JSON 포맷으로 별도 <script>에 삽입 |
| 사용 용도/장점 | 간단한 구조화된 표현 |  유연성, 메타데이터와 통합이 용이 | html 과 분리되어 관리 용이 |

https://www.next-t.co.kr/seo/seo-wiki/%EA%B5%AC%EC%A1%B0%ED%99%94%EB%90%9C-%EB%8D%B0%EC%9D%B4%ED%84%B0/ - 참고문서

### Q2.
> 박근영
> 마이크로데이터 속성 중 itemid와 itemref에 대해서도 알려주세요

### A2.
- itemid : 고유 식별자로, html의 id와 유사한 역할을 합니다. 하지만, 책에서 id 보다는 class 사용을 권했듯, 마찬가지로 itemprop 
사용을 권장합니다. (131p)
- itemref : itemtype 문서에 정의된 itemprop들을 하나의 항목으로 묶어서 연결하고 싶을 때 사용하는 속성입니다.
예를 들면, 아래 경우는 itemref가 id가 address인 태그를 참조해, 정의된 PostalAddress가 Person에 속한 정보임을 알려줍니다.
```
<div itemscope itemtype="http://schema.org/Person" itemid="https://example.com/person/johndoe" itemref="address">
  <span itemprop="name">John Doe</span>
  <span itemprop="telephone">+1234567890</span>
</div>

<div id="address" itemscope itemtype="http://schema.org/PostalAddress">
  <span itemprop="streetAddress">123 Main St</span>
  <span itemprop="addressLocality">Anytown</span>
</div>
```
https://html.spec.whatwg.org/multipage/microdata.html#attr-itemref - itemref

### Q3.
> 임해찬
> 마이크로데이터는 rel 속성 대신에 itemprop 속성과 itemtype 속성을 통해 리소스의 관계를 표현하는 것으로 보이는데,
> itemprop 속성과 itemtype 속성을 통해 리소스의 관계를 표현하는 예시를 보고 싶습니다.

### A3.
먼저 rel과 마이크로데이터의 차이를 알면 더 이해하기 쉬운데, rel의 경우에는 현재 문서와 다른 문서의 관계를 정의하는 것으로 1:1의 관계입니다. 하지만, 마이크로데이터는 구조화된 데이터에 가깝습니다. 저는 마이크로데이터를 HTML을 JSON 처럼 만들어주는 것으로 해석했습니다.
결과적으로, itemtype은 데이터의 유형을 정의하는 문서이고, itemprop은 해당 문서의 특정 데이터 항목을 지정하는 용도입니다.
우리가 특정 JSON 데이터를 가져와서, 특정 속성의 값을 받아와 사용하는 것과 비슷하게 보면 될 것 같습니다.
```
rel 예시
<a href="next-article.html" rel="next">Next Article</a>
<a href="previous-article.html" rel="prev">Previous Article</a>
```
```
마이크로데이터 예시
<div itemscope itemtype="http://schema.org/Person">
  <span itemprop="name">John Doe</span>
  <span itemprop="telephone">+1234567890</span>
</div>
```

### Q4.
> 최준영
> 리뷰, 이벤트, 사용자 프로필 등의 구조화된 데이터의 여러가지 타입들에 대해 소개해주세요

### A4.
hCard 와 같은 표준을 말씀하시는 것 같습니다. hCard 와 같은 마이크로포맷에는 h-adr(adr의 upadate ver.) : 주소, h-recipe(hRecipe의 update ver.): 레시피, h-resume(hResume의 update ver.): 이력서 등이 있습니다.
https://microformats.org/wiki/microformats2#Parsers - microformats2


### Q5.
> 곽재영
> HTML 4의 rev 속성을 rel 속성과 비교하여 설명해주시고, 현재는 왜 사용하지 않는 지 자세한 이유를 알고 싶어요.

### A5.
130p 주석에 rev 속성은 rel의 반대에 해당한다고 합니다. rel은 next, rev는 previous의 관계를 나타내는 역할을 한다고 합니다.
html5에서 제외된 이유는 rev가 rel에 비해 많이 사용되지 않았고, 관계는 한 방향으로 나타내는 편이 직관적이고 명확했기 때문이라고 합니다.
https://www.w3.org/TR/html4/struct/links.html#h-12.3.1 - what is rel & rev
https://www.w3.org/TR/html5-diff/#obsolete-attributes - obsolete attributes

### Q6.
> 서형석
> AI를 활용해서 HTML의 새 하이퍼미디어 컨트롤이나, 프로토콜 의미 체계, 애플리케이션 의미 체계를 확장할 수 있을까요?
>본인의 생각을 먼저 정리한 뒤, LLM에도 질문을 던져보고, 예시 두 가지를 소개해주세요 :)
>
>(extra. 이를 통해 스스로 발전하는 웹 통신 표준을 만들어 볼 수 있을까요?)

### A6.
제가 해석한 Rest의 의미는 결국, 자동화를 가능케하는 것이 목적인 것 같습니다. 사람처럼 name이나 title, discription 등에서 의미를 찾아
내지 못하고 그저 0과 1로만 이해하는 컴퓨터를 위해서. 하지만, 그건 전통적인 소프트웨어 에이전트에 해당하는 내용일 것 같습니다.
AI를 이용한다면, 이렇게 제약을 하면서 패턴을 만들 필요 없이 클라이언트와 동일한 이해를 가지지 않을까요? 결국, 제약이 없기 때문에 동시
에 프로파일 문서 하나만 주면 알아서 처리해주는 에이전트가 생기기 때문에 확장성이 크게 높아질 것 같습니다.

>chatGPT 4o mini
>AI를 활용하여 HTML의 새 하이퍼미디어 컨트롤, 프로토콜 의미 체계, 애플리케이션 의미 체계를 확장할 수 있는 가능성은 충분히 존재합니다. 
>이 과정은 AI가 제공하는 패턴 인식, 자동화, 예측 기능을 활용하여 기존의 웹 표준을 발전시키고, 더 나아가 새로운 형태의 상호작용 모델을 
>구현할 수 있는 방향으로 발전할 수 있습니다.
>
> 1. 하이퍼미디어 컨트롤의 확장 (AI 기반 링크 및 콘텐츠 추천)
> - AI 기반 링크 및 콘텐츠 추천 : 사용자의 이전 동작, 검색 기록, 클릭 패턴, 혹은 유사한 사용자들의 행동을 분석해 연관된 페이지, 리소스를 제시할 수 있습니다.
> - 동적인 하이퍼미디어 요소 생성 : AI가 실시간으로 웹 페이지의 내용을 분석하고 그에 맞는 링크를 자동으로 생성할 수 있습니다.
> 
> 2. 프로토콜 의미 체계의 확장 (AI 기반 네트워크 최적화 및 예측)
> - 네트워크 최적화 : HTTP 요청 최적화, 데이터 압축, 요청 분산, 트래픽 병목 회피
> - 프로토콜 개선 : 패킷 손실, 지연, 오류 등을 모니터링하여 적응형 프로토콜 설계
> - 자기 학습형 프로토콜 : 기존 프로토콜을 학습해 네트워크 상황에 맞춰 프로토콜을 스스로 개선
>    
> 3. 애플리케이션 의미 체계의 확장 (AI 기반 UI/UX 최적화 및 예측)
> - 동적인 UI/UX 최적화 : 사용자 행동을 분석해 최적의 UI/UX 디자인 조정
> - 개인화된 경험 제공 : 사용자의 취향 등을 분석해 콘텐츠를 개인화
> - 자동화된 애플리케이션 흐름 조정 : 애플리케이션 사용 중 필요한 데이터를 예측해 자동 제공







