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

1. 보안 문제 - 자동 클라이언트에는 고유한 API key를 발급하여 요청을 보내게 하고, 일반 유저는 API key 없이 JWT, OAuth2 등의 방식으로 인증을 하는 방식을 통해 자동 클라이언트와 유저를 명시적으로 구분하는 방법을 사용해 볼 수 있습니다.
2. 요청 횟수 문제 - 자동 클라이언트 요청에 API key를 포함한다면, 이를 통해 요청 횟수를 제한하거나 요청 횟수에 따른 요금 부여 등의 방식으로 제어할 수 있습니다.

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

하이퍼미디어에 기반을 둔 설계는 REST 원칙 중 하나인 **HATEOAS(Hypermedia as the Engine of Application State)** 를 잘 따르는 것이라고 볼 수 있습니다.

이는 간단하게 클라이언트가 응답에서 제공되는 링크를 통해 어떤 동작을 수행할 수 있는지 알 수 있어야하고, 이를 통해 다음 동작을 수행해야 한다는 원칙입니다.

```json
{
    "id": 100,
    "subject": "article 100",
    "content": "content",
    "_links": {
        "self": {
            "href": "/api/articles/100"
        },
        "next": {
            "href": "/api/articles/101"
        },
        "like": {
            "href": "/api/articles/likes"
        },
        "comment": {
            "href": "/api/articles/100/comments"
        },
        "home": {
            "href": "/"
        }
    }
}
```

이런식으로 API를 설계한다면 서버의 응답이 변경되었다 하더라도, 클라이언트에서 어떤 동작들을 할 수 있고 그 동작을 하려면 어떤 url로 요청을 보내야하는지까지 알 수 있어 하이퍼미디어에 기반을 둔 설계는 더 유연하다고 볼 수 있습니다.

**+ 추가**

그렇다면 왜 현대 개발에서 REST API를 설계할 때 HATEOAS 원칙을 잘 지키지 않을까?

![image](https://github.com/user-attachments/assets/63696982-380d-4351-a691-9d532d3b01d5)

REST API 구현 단계

- 레벨 0: HTTP 프로토콜을 사용하여 API를 구현하지만 모든 기능을 활용하지는 않는다. 또한 리소스에 대한 고유 주소는 제공되지 않는다.
```
method: POST URI: /movie
```

- 레벨 1: 리소스에 대한 고유 식별자가 있지만 리소스에 대한 각 작업에는 고유한 URL이 있다.
```
method: POST URI: /movie/1/delete
```

- 레벨 2: 동작을 설명하는 동사 대신 HTTP 메소드를 사용한다.
```
method: DELETE URI: /movie/1
```

- 레벨 3: HATEOAS 원칙을 지킨다. 리소스에 하이퍼미디어를 도입하며, 이를 통해 가능한 작업에 대해 알려주는 응답에 링크를 배치할 수 있으므로 API를 통해 탐색할 수 있는 가능성이 추가된다.
```
method: DELETE URI: /movie/1
```

<br/>

레벨 3 까지의 엄격한 REST API 설계를 가져가지 않는 이유는 다음과 같을 것입니다.

**1. 구현의 복잡성 증가**

레벨 2 까지는 단순히 url이나 리소스, 동작에 대한 네이밍 규칙 정도이므로 추가적인 구현이 대부분 필요하지 않습니다.

하지만, 레벨 3의 HATEOAS 원칙을 지키려면 응답에 추가적인 구현이 필요하게 됩니다.

(spring에는 spring hateoas 라이브러리가 있다고 합니다. nestjs에는 없는듯?)

**2. 필요성 부족**

대부분의 개발(서비스 개발에 한정)에서 클라이언트와 서버는 거의 1대 1 관계라고 볼 수 있습니다.

또, 공개된 API가 아니라 특정 프로젝트에서만 사용되는 private API인 경우가 대부분입니다.

즉, 클라이언트는 서버에 정의된 API를 사전에 알고 개발되고, 서버 측에서는 Swagger와 같은 문서화 도구를 통해 API 엔드포인트의 집합을 클라이언트에게 제공합니다.

따라서, 클라이언트에게 가능한 동작들을 응답에 포함하는 HATEOAS 원칙의 필요성이 부족하다고 볼 수 있습니다.

**3. 추가적인 네트워크 요청**

HATEOAS 원칙으로 설계된 API에서 특정 리소스에 어떠한 동작을 하고 싶다면, 일단 그 리소스를 GET으로 호출한 뒤 응답을 통해 원하는 동작을 찾아 다시 해당 주소로 요청을 보내야 합니다.

즉, 하나의 동작을 하는데 여러번의 네트워크 요청이 발생하게 됩니다.

문서화 등을 통해 관리되는 경우에는 한 번의 요청으로 동작을 처리할 수 있는 것에 반해, 속도나 성능 면에서 비효율적이라고 볼 수 있습니다.

<br/>

**Q4.**
> 5장에서 언급된 IANA (Internet Assigned Numbers Authority)에서 관리하는 연결 관계(Link Relations) 등록부와 더불어, IANA에서 관리하는 것들 중 RESTful 개념과 관련된 것을 추가로 1개 이상 소개해주시고 어떻게 관련이 있는지 설명해주시길 바랍니다.
>
> `by 박지환`

**A4.**

![image](https://github.com/user-attachments/assets/fa2f1e34-0072-4aca-9656-3f50a4491235)

[IANA](https://www.iana.org/)는 인터넷에서 사용하는 다양한 프로토콜, 번호, 이름 등을 관리합니다.

IANA에서 관리하는 것 중 RESTful 과 관련된 것은 크게 다음과 같습니다.

**1. 미디어 타입**

RESTful API에서 클라이언트와 서버 간 데이터 교환 시 데이터 포맷을 정의합니다.

ex) `application/json`, `text/html` 등

https://www.iana.org/assignments/media-types/media-types.xhtml

**2. HTTP 상태 코드**

RESTful API 응답에서 사용되는 HTTP 상태 코드는 IANA가 정의하고 관리합니다.

https://www.iana.org/assignments/http-status-codes/http-status-codes.xhtml

**3. 표준 헤더**

RESTful API에서 클라이언트와 서버 간에 추가 정보를 전달하는 데 사용되는 HTTP 헤더도 IANA가 관리합니다.

ex) `Content-Type`, `Authorization`, `Accept` 등

https://www.iana.org/assignments/message-headers/message-headers.xhtml
