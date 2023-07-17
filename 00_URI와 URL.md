# 00_URI와 URL

> URI와 URL의 구분

<img src="https://github.com/siwon-park/Problem_Solving/assets/93081720/419c9100-0072-4cce-b124-5e434e1b0997" referrerpolicy="no-referrer" alt="image" height= "500px">

- URI: 자원의 식별자(Identifier)
- URL: 자원의 위치(Location)
- URN: 자원의 이름(Name)

<br>

## 1. URI

> 통합 자원 식별자; Uniform Resource Identifier

인터넷에 있는` 자원 자체를 식별`하는 방법

URI는 인터넷에서 요구되는 `기본조건`으로서 인터넷 프로토콜에 항상 붙어있음

하위 개념으로 URL, URN이 있다.

예를 들어, `https://www.naver.com/index.html?page=1232950&id=776` 전체를 URI라고 한다.

<br>

## 2. URL

> Uniform Resource Location

인터넷 상에서 `자원이 어디에 위치해 있는지` 위치를 알려주기 위한 규약

즉, 컴퓨터 네트워크와 메커니즘에서의 위치를 지정하는 웹 리소스에 대한 참조를 의미한다.

★☆ 흔히 URL을 웹 사이트 주소로 알려져 있지만, `웹 사이트 주소뿐만 아니라 네트워크 상의 자원을 모두 나타내는 표기법`임

예를 들어, `https://www.naver.com/index.html?page=1232950&id=776`라는 URI가 있을 때,
`https://www.naver.com/index.html`를 URL이라고 한다.

<br>

## 3. URN

> Uniform Resource Name

통합 자원 이름으로, 리소스에 이름을 부여하는 것이다.

하지만 리소스가 이름으로 맵핑되어 있으면 찾기가 힘들다. 왜냐하면 맵핑된 이름을 알아야 하기 때문이다.

따라서 대부분 URL만 사용한다. URN은 크게 신경 쓸 필요 없다.

<br>