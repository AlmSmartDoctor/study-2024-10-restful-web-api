# 1장. 웹 서핑하기


### 1-0. REST란?

### 1-1. Restful Api 


### 1-2. 리소스와 표현

### 1-3. 의미 체계의 문제
  - '트위터 API, 페이스북 API, 구글+ API ... 이 세 API는 비슷한 일을 수행한다. ... 하지만 각 API는 완전히 다른 디자인을 갖고있다.' (xvi)
  - '웹 API의 요점은 사람이 온종일 웹 브라우저 앞에 앉아서 모든 결정을 내리지 않아도 동작한다' (18p)
  - 저자가 RESTful 아키텍쳐를 도입하고자 하는 이유.

### 1-4. Application State & Resource State
  - 애플리케이션 상태란 : 전체 시스템이나 클라이언트의 현재 상태.
Application state is information about where you are in the interaction. It is used during your session with an application. For example, the fact that you are viewing picture 23, or the fact that you are logged in on Twitter, are both application state. Changes to this state are possible by the controls in hypermedia representations. In the pictures case, they are links, and on Twitter, they are the tweet box and button.
  - 예시 : 사용자가 로그인했는지, 현재 페이지에서 어떤 데이터를 표시하는 지 등

  - 리소스 상태란 : 특정 자원의 현재 상태. 일반적으로 API에서 다루는 데이터 객체(데이터와 해당 데이터의 메타데이터)
Resource state is the kind of (semi-)permanent data a server stores, and lasts beyond the duration of a single session of interactions. An image that has been uploaded to the gallery and a tweet you have sent are examples of resource state. So, as you can see, although HTTP is a stateless protocol, you can still have long-term state on the server. However, the short-term state that is used during your interaction with the server is solely your client’s responsibility, and it has to send it within every request. This makes the web stateless and therefore scalable.
  - 예시 : 홈페이지 HTML 문서, 이미지, 비디오 등의 파일, JSON 데이터 등.

  - 애플리케이션 상태와 리소스 상태의 혼재 : 일반적으로 애플리케이션 상태는 클라이언트 측에서 관리하고, 리소스 상태는 서버에서 관리하지만, 서버가 애플리케이션 상태를 나타내거나 클라이언트가 HTML 폼 등을 보내서 리소스를 조작하기도 한다.

질문 목록 - 
어플리케이션과 리소스 상태가 정확히 어떤 것을 나타내는 것이고,
그리고 둘이 뒤섞여 있는 경우는 어떤 경우를 의미할까요?

책에 HEAD HTTP 요청에 대해 소개되었는데, 해당 요청의 기능에 대해 알려주세요.

GET요청은 리소스를 변경하지 않는다고 하는데, GET요청을 통해 서버 리소스를 변경하도록 한다면 그건 바람직하지 않다고 하나요?

이 책에서는 웹이 URL 명명 규칙, HTTP 프로토콜, HTML 문서 형식의 세 가지 기술로 이루어져 있다고 본다고 하는데,
그럼 다른 곳에서는 다르게 분류하는 곳도 있는건가요? 있다면 어떻게 분류할 수 있을까요? - 목요일 조

책에서 http와 다른 프로토콜들의 '세션' 유지기간에 대해서 비교하여 설명하고 있는데, 여기서 세션이란 어떻게 정의되는 것일까요?

SOAP 표준과 REST 표준의 핵심적인 차이점은 무엇인가요?
또한, REST 표준이 탄생하게 된 결정적인 이유는 무엇인가요?
