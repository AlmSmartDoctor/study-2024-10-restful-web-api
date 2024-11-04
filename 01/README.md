# 1장. 웹 서핑하기


### 1-0. REST란?

> "REST is a hybrid style derived from several of the network-based architectural styles described in Chapter 3 and combined with additional constraints that define a uniform connector interface." [Fielding, R. T. (2000)](https://ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm)

\- **REST란?** 여러 아키텍쳐 스타일에서 파생된 혼합 스타일로, 추가적인 제약을 결합한 것.

\- Representational State Transfer - 자원을 표현으로 구분하여 해당 자원의 상태를 주고 받는 것.

### 1-1. RESTful API 

\- **RESTful API란?** REST라는 표준을 따르는 API(Application Programing Interface)

- API란 정의 및 프로토콜 집합을 사용해 두 소프트웨어 구성 요소가 서로 통신할 수 있게 하는 메커니즘 (중간 전달자) [aws](https://aws.amazon.com/ko/what-is/api/)
- RESTful 이전의 API의 문제 **한 번 배포되면 바꾸기 어렵다**

---

### 1-2. 리소스와 표현

\- **resource & representation** 리소스는 URL이 붙은 것(3p) - 일반적으로 리소스는 특정 자원으로 주로 데이터 객체를 의미하는데, 결국 REST 아키텍쳐에서 리소스는 웹에서 고유하게 식별되고 접근될 수 있어야 하기 때문에 그렇게 표현한 것 같습니다.
그리고 표현(representation)은 리소스에(URL) HTTP 요청을 보내면 응답으로 보내는 것을 뜻합니다. 책에서는 HTML 문서를 예로 들고 있지만, 다양한 형태의 하이퍼미디어가 전부 포함될 수 있죠.

api란 우리가 리소스와 표현을 주고 받는 통신 메커니즘이고, REST는 리소스의 형태, 표현의 형태, 통신 방식의 디자인.



### 1-3. 의미 체계의 문제
  - '트위터 API, 페이스북 API, 구글+ API ... 이 세 API는 비슷한 일을 수행한다. ... 하지만 각 API는 완전히 다른 디자인을 갖고있다.' (xvi)
  - '웹 API의 요점은 사람이 온종일 웹 브라우저 앞에 앉아서 모든 결정을 내리지 않아도 동작한다' (18p)
  - 저자가 RESTful 아키텍쳐를 도입하고자 하는 이유.

---

### 질문 1. Application State & Resource State

\- **애플리케이션 상태란** : 전체 시스템이나 클라이언트의 현재 상태.
- 예시 : 사용자가 로그인했는지, 현재 페이지에서 어떤 데이터를 표시하는 지 등

\- **리소스 상태란** : 서버가 저장하는 특정 자원의 현재 상태. 일반적으로 API에서 다루는 데이터 객체(데이터와 해당 데이터의 메타데이터)
  - 예시 : 홈페이지 HTML 문서, 이미지, 비디오 등의 파일, JSON 데이터 등.

\- **애플리케이션 상태와 리소스 상태의 혼재** : 일반적으로 애플리케이션 상태는 클라이언트 측에서 관리하고, 리소스 상태는 서버에서 관리하지만, 서로 상호작용하는 경우 존재. 
예를 들어, 사용자가 프로필을 편집하는 과정에서 현재 편집 중인 내용은 애플리케이션 상태이지만, 변경사항을 저장하게 되면, 리소스 상태가 된다. 
SNS에서 게시물을 스크롤하고 있을 때, 보고 있는 게시물은 애플리케이션 상태이지만, 게시물 자체(작성자, 내용, 좋아요 수 등)는 리소스 상태.

[Ruben verborgh](https://ruben.verborgh.org/blog/2012/08/24/rest-wheres-my-state/)


### 질문 2. HEAD HTTP 요청

>"The HEAD HTTP method requests the metadata of a resource in the form of headers that the server would have sent if the GET method was used instead." [mdn web docs](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/HEAD)

\-HEAD HTTP 메소드는 리소스의 메타데이터를 헤더 형식으로 요청합니다. GET 메소드에서 얻을 수 있는 헤더와 동일한 정보를 제공.
- 언제쓰이는가? : URL이 대량의 다운로드를 생성할 경우, HEAD를 사용해 파일 크기를 미리 확인 가능.

### 질문 3. GET 요청으로 리소스를 변경한다면?

\- **GET은 가장 흔한 HTTP메소드** : 일단 웹 브라우저는 get을 기본값으로 가집니다. 또한 23p에서 말하듯, '클라이언트가 무엇이 있는지 파악하기 위해 URL에 GET 요청을 자주 날린다는 사실'을 인지해야 합니다. 가장 일반적이고, 흔한 요청이기 때문에 리소스를 변경하게 된다면, 데이터의 일관성을 보장하기 어려울 것 같습니다. 예를 들면, 유튜브 조회수가 get요청을 받을 때마다 무조건 올라가도록 만들었다면...?
물론 그 외에도, get은 url에 모든 데이터가 포함되므로, 개인 정보를 전달하기에 적합하지 않고, 길이에도 한계가 있습니다. 

### 질문 4. 세션

\-**REST에서의 세션** : 전통적인 세션은 서버가 클라이언트의 상태(애플리케이션 상태)를 저장하고 유지했습니다. 
REST에서는 클라이언트가 애플리케이션 상태를 기억하게 했습니다. 그 결과, 클라이언트는 모든 요청마다 필요한 정보들을 함께 전송해야 합니다. 서버는 더 이상 클라이언트의 상태를 기억하지 않습니다. 이를 무상태(stateless)라고 부릅니다.
그래서 REST에서는 요청과 응답이 이루어지는 단기적인 상호작용을 '짧은 세션'으로 봅니다.

- 예를 들어서, 사용자가 10번 게시글을 보다가 다음으로 넘어가려면 서버에게 '다음'이라고 말하는 것이 아닌, 11번 게시글을 요청해야 합니다. 서버는 현재 사용자가 어떤 글을 읽고 있는 지 모르는 상황인 것. 하지만 11번 게시글을 줄 때, 제어(링크)를 전송하는 방식으로 사용자가 '다음'에 접근할 수 있게 해줍니다.(하이퍼미디어)

- 웹의 확장성에 크게 기여. 모든 요청을 동일한 방식으로 처리, 리소스 절약.

### 질문 5. SOAP 표준과 REST 표준

### 질문 6. 웹

질문 목록 - 



이 책에서는 웹이 URL 명명 규칙, HTTP 프로토콜, HTML 문서 형식의 세 가지 기술로 이루어져 있다고 본다고 하는데,
그럼 다른 곳에서는 다르게 분류하는 곳도 있는건가요? 있다면 어떻게 분류할 수 있을까요? - 목요일 조

책에서 http와 다른 프로토콜들의 '세션' 유지기간에 대해서 비교하여 설명하고 있는데, 여기서 세션이란 어떻게 정의되는 것일까요?

SOAP 표준과 REST 표준의 핵심적인 차이점은 무엇인가요?
또한, REST 표준이 탄생하게 된 결정적인 이유는 무엇인가요?
