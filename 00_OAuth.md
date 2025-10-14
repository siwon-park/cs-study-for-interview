# 00_OAuth (Open Authorization)

> Open Authorization

인터넷 사용자들이 비밀번호를 제공하지 않고 다른 웹사이트나 어플리케이션의 자신의 정보에 대해 접근 권한을 부여할 수 있는 공통적인 수단으로서 사용되는, 접근 위임을 위한 개방형 표준이다.

OAuth의 Auth가 `인가(Authorization)`임을 유의하자. (그래서 "접근 위임"을 위한 표준인 것이다.)

- 인가는 요청한 사용자가 `권한`이 있는 사용자인지 확인하는 것
- OAuth의 목표는 다른 어플리케이션이나 서비스에 접근할 수 있도록 사용자에게 권한을 부여하는 것
- 단, 이러한 과정이 `올바른 사용자`인지 확인하는 `인증(Authentication)` 과정이 없다고만 볼 수는 없기 때문에 OAuth를 "인증 방식"이라고 부르는 것임

<br>

## 1. 개요

### 1) 기존의 인증 방식

아이디와 비밀번호를 사용한 인증 방식

- 해킹의 위험 때문에 보안적으로 안전하지 않음 => 서비스의 보안 수준이 낮다면 해커가 DB를 털어버리면 ID와 PW가 다 털리게 됨.
  - 신뢰: 사용자가 애플리케이션에 ID/PW를 제공하기 꺼려함
  - 피싱에 둔감해짐: 각 종 애플리케이션들에 ID/PW 를 계속 제공하는 경우
  - 접근범위가 늘어남에 따른 위험 부담: ID/PW를 모두 알고 있는 애플리케이션은 모든 권한을 가짐
  - 신뢰성의 제한: PW 를 변경한다면 애플리케이션은 동작을 하지 못하게 됨
  - 폐기문제: 권한을 폐기할 수 있는 유일한 방법이 PW를 변경하는 것

이에 대한 대안으로 각 어플리케이션마다 각 회사의 인증 방식을 도입하기 시작함

- 그러나 이 인증 방식들은 통합된 표준이 없이 개별적인 인증 API였음

<br>

### 2) OAuth의 등장

이렇게 개별적인 인증 방식을 통합하고 표준화한 인증 방식이 `OAuth`이다.

OAuth의 등장으로 인해 OAuth 인증 방식을 이용하는 어플리케이션 간에는 별도의 다른 인증 과정이 필요 없어졌다.

따라서 어플리케이션의 통합 사용이 가능한 시대가 열린 것이다.

#### (1) OAuth 1.0

트위터(Twitter)의 주도 하에 OAuth 1.0이 탄생함.

그러나 OAuth 1.0은 구현이 복잡하고, 웹에 친화적이다보니 어플리케이션에 대한 지원이 부족하다는 한계가 있었음.

또한 AccessToken이 만료되지 않는 문제도 있었다.

이외에도 다른 한계점들을 극복하기 위해 등장한 것이 `OAuth 2.0`이다.

<br>

## 2. OAuth 2.0

### 1) OAuth 1.0과의 차이점

- 기능의 단순화, 기능과 규모의 확장성 등을 고려하여 만들어짐
- OAuth 1.0은 만들어진 이후 표준이 되었지만, OAuth 2.0은 표준으로서 만들어짐
- https로 암호화되며 필수가 됨
- 인증 방식이 한 가지였던 OAuth 1.0에 비해 OAuth 2.0은 다양한 인증 방식을 제공함
- API 서버에서 인증 서버를 분리할 수 있도록 구성함

<br>

### 2) OAuth 2.0 주요 구성 요소

OAuth 주체와 관련 용어

#### (1) Resource Owner

서비스 사용자(리소스 사용자)로, 해당 서비스나 어플리케이션을 이용하면서 구글, 페이스북, 트위터 등의 플랫폼에서 리소스(개인 정보 데이터)를 보유하고 있는 사용자이다.

#### (2) Client

서비스 사용자(Resource Owner)의 자원을 이용하고자 하는 서비스(Client)로 보통 웹 서비스, 어플리케이션 등을 의미한다. 

#### (3) Authorization Server

서비스 사용자(Resource Owner)를 인증하고 서비스(Client)에게 엑세스 토큰(Access Token)을 발급해주는 서버

#### (4) Resource Server

구글, 페이스북, 트위터, 네이버, 카카오와 같이 개인 정보 데이터, 자원(Resource)를 가지고 있는 서버, 플랫폼을 말한다.

#### (5) Redirect URI

인가가 허용된 사용자가 이동할 자원의 식별자(URI), 즉 이동할 곳을 의미.

OAuth 2.0 서비스는 인증이 성공한 사용자를 사전에 등록된 Redirect URI로만 리다이렉트 시킨다.

만약 승인되지 않은 URI로 리다이렉트될 경우, 인가 코드(Authorization Code)를 탈취하여 공격자가 사용자 계정에 접근할 수 있는 위험이 있기 때문에 사전에 승인된 URI만 허락하는 것이다.

기본적으로 Redirect URI는 보안을 위해 https만 허용되지만 localhost는 http가 허용된다.

#### (6) Client ID, Client Secret

Client ID와 Client Secret은 Access Token을 획득하는데 사용된다.

Client ID는 클라이언트 웹 어플리케이션을 구별할 수 있는 식별자 ID이고, Client Secret은 Client ID에 대한 비밀키이다. 따라서 Client ID는 공개되어도 상관없지만, Client Secret은 절대 유출되어서는 안 된다.

<br>

### 3) OAuth 2.0 동작 메커니즘

OAuth 2.0의 동작 메커니즘은 다음과 같다.

![image](https://github.com/siwon-park/BackEnd_Study/assets/93081720/da2fc1d0-3e4a-4fe6-b6b1-992177cb0be8)

<br>

각 단계별로 추가적인 설명이 필요한 부분만 따로 정리하여 설명함.

#### 1 ~ 2 단계 로그인 요청

사용자(Resource Owner)는 어플리케이션에 접근하여 서비스를 이용하기 위해 `"OO으로 로그인하기" ` 버튼을 누른다.

그러면 클라이언트(Client)는 OAuth 인증 프로세스를 시작하고 이 요청을 Authorization Server로 보낸다.

이 때, 클라이언트는 `response_type`, `client_id`, `redirect_uri`, `scope`등의 매개변수를 쿼리 스트링으로 포함해서 보낸다.

- 예시

```http
https://authorization-server.com/auth?response_type=code
&client_id=29352735982374239857
&redirect_uri=https://example-app.com/callback
&scope=create+delete
```

- `response_type`: 반드시 `code`로 설정
- `redirect_uri`: 리다이렉트 URI
- `client_id`: 클라이언트 식별자
- `scope` : 클라이언트가 부여받은 리소스 접근 권한

<br>

#### 5 ~ 6 단계 Authorization Code 발급, Redirect URI로 리다이렉트

인증이 성공되었다면 미리 등록된 Redirect URI로 리다이렉트 된다.

이 때, Redirect URI에는 `Authorization Code를 포함하여 리다이렉트`시킨다. 구글의 경우 쿼리 스트링에 포함시킨다.

- Authorization Code란 클라이언트가 Access Token을 획득하기 위해 사용하는 임시 코드이다. 이 코드는 수명이 매우 짧다. 일반적으로 1분 ~ 10분이다.

<br>

#### 7 ~ 8 단계 Authorization Code와 Access Token 교환

클라이언트는 발급받은 Authorization Code를 Authorization Server에 전달하고 Access Token을 응답받는다.

클라이언트는 사용자의 Access Token을 발급받고 나면 따로 저장하고, 이후 Resource Server(혹은 API 서버)에 사용자의 리소스에 접근하기 위해 Access Token을 사용한다. Access Token만 사용하면 안 되기 때문에 Refresh Token도 따로 발급해서 클라이언트에 전달해주기도 한다.

- Access Token은 유출되면 안 되기 때문에 HTTPS 연결을 통해서만 사용할 수 있다.

Authorization Code와 Access Token 교환은 `token` 엔드 포인트에서 이루어진다. 예시는 다음과 같다.

```http
POST /oauth/token HTTP/1.1
Host: authorization-server.com

grant_type=authorization_code
&code=xxxxxxxxxxx
&redirect_uri=https://example-app.com/redirect
&client_id=xxxxxxxxxx
&client_secret=xxxxxxxxxx
```

- `grant_type` : `authorization_code` 로 설정한다.
  - Implicit Grant, Resource Owner Password Credentials Grant, Client Credentials Grant 방식이 있지만, 보통 Authorization Code Grant를 많이 사용한다.
- `code` : 발급받은 Authorization Code
- `redirect_uri` : 리다이렉트 URI
- `client_id` : 클라이언트 식별자
- `client_secret`: 클라이언트 식별자에 대한 비밀키. RFC 표준은 아니지만 발급된 경우에는 포함하여 요청해야 한다.

<br>

### 4) OAuth의 스코프(Scope)

최초에 로그인을 요청할 때, `scope`라는 내용을 함께 요청하는 것을 확인하였다.

스코프(범위)를 설정하는 이유는 유저 리소스에 대한 클라이언트의 접근 범위를 제한하기 위해서이다.

스코프는 여러 개가 될 수 있으며, 대소문자를 구분하는 문자열로 공백으로 구분되어 표현한다. 이 때, 문자열은 OAuth 2.0 인증 서버에 의해 정의된다.

<img src="https://github.com/siwon-park/BackEnd_Study/assets/93081720/13cba2b0-abfe-4649-a449-92e4acd03206" referrerpolicy="no-referrer" alt="image" width = 50%>

<br>

### 5) Authorization Code의 필요성?

굳이 클라이언트에게 직접 Access Token을 주지 않고 왜 Authorization Code를 발급하고 교환하는 번거로운 절차를 추가하는 것일까?

만약 Redirect URI를 통해 Authorization Code를 발급하는 과정이 생략된다면, Authorization Server는 Access Token을 클라이언트에게 전달하기 위해 직접 Redirect URI를 통해야 한다.

이 때, Redirect URI를 통해 데이터를 전달할 방법은 URL 자체에 데이터를 실어서 전달하는 방법밖에 존재하지 않는다. Access Token과 같이 민감한 정보를 이런 방식으로 전달한다면 쉽게 노출되어 보안 사고가 발생할 것이다.

그래서 Redirect URI를 프론트엔드 주소로 설정하고 Authorization Code를 프론트 쪽에 전달한 뒤, 다시 프론트에서 백엔드로 전달하고, 백엔드에서 Authorization Code를 Authorization Server로 token 엔드 포인트로 요청하여 Access Token을 발급 받는 것이다.

번거로운 과정이지만, 중요한 데이터의 교환을 서버의 뒷 단에 숨겨서 보안적인 이점을 취하는 것이다.
