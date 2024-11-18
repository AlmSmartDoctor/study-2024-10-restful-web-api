# 5장. 도메인 특화 설계 (~ 클라이언트는 그들이 원하는 일을 한다)

[maze+xml format](http://amundsen.com/media-types/maze/format/)

[maze-client example](http://amundsen.com/examples/misc/maze-client.html)

## Q&A

**Q1.**
> 자동 클라이언트가 서버와 통신하면서 생길 수 있는 문제들은 어떻게 해결하면 좋을까요?
>
> 1. 보안 문제 - 자동 클라이언트와 유저를 서버가 구분하여 서로 다른 보안 수준을 제공해야 할까요?
> 2. 요청 횟수 문제 - 자동 클라이언트는 일반 유저보다 많은 요청을 할 확률이 높은데, 서버 측에서는 어떻게 처리하는 것이 좋을까요?
>    
> `by 정예찬`

**A1.**

<br/>

**Q2.**
> 책에서 가상으로 소개된 north, west, east, south 외에 자주 쓰이는 rel 키워드가 존재하는지, 있다면 무엇이 있을지 궁금해요
>
> `by 공병규`

**A2.**

일단, north, west, east, south의 값들은 책에서 가상으로 소개된 값들이 아니라, 실제 maze+xml에 유효한 `rel` 속성 값으로 정의된 값들입니다.

아래는 maze+xml 문서의 `rel` 속성에 대해 유효한 모든 값 목록입니다.


<img width="1023" alt="image" src="https://github.com/user-attachments/assets/32427de4-d73d-4979-87c3-18ac343b0094">


<br/>

**Q3.**
> 78p에서 하이퍼미디어에 기반을 둔 설계는 더 유연하다. 라고 언급하고 있는데, 하이퍼미디어가 어떻게 시스템의 유연성을 제공하는지 구체적으로 설명해주세요.
>
> `by 정시우`

**A3.**

HATEOAS

<br/>

**Q4.**
> 5장에서 언급된 IANA (Internet Assigned Numbers Authority)에서 관리하는 연결 관계(Link Relations) 등록부와 더불어, IANA에서 관리하는 것들 중 RESTful 개념과 관련된 것을 추가로 1개 이상 소개해주시고 어떻게 관련이 있는지 설명해주시길 바랍니다.
>
> `by 박지환`

**A4.**
