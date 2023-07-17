# 00_주소창에 URL를 입력하면 일어나는 일

> 사용자가 웹 브라우저의 주소창에 URL을 입력하면 무슨 일이 발생할까?

## 1. 개요

![image](https://github.com/siwon-park/Problem_Solving/assets/93081720/29150302-340d-4ec6-9094-31c9a921a027)

<br>

### 1) 과정

위 그림 번호와 순서가 다를 수 있으나 큰 과정에서는 일치한다.

#### ① 사용자가 웹 브라우저 주소창에 URL을 입력한다

#### ② 웹 브라우저는 DNS 서버를 통해 도메인 주소와 맵핑된 IP 주소를 확인한다

DNS(Domain Name Server)는 URL들의 이름, 도메인 주소와 IP 주소를 저장하고 있는 서버이다. 해당 서버를 통해 도메인 주소에 해당하는 IP를 찾기 전에 우선 캐싱된 데이터가 있는지 확인한다.

- 가장 먼저 브라우저의 캐시를 확인한다.
- OS의 캐시를 확인한다.
- 라우터의 캐시를 확인한다.
- 마지막으로 ISP의 캐시를 확인한다.\

요청된 URL이 캐시에 없으면 `ISP(Internet Service Provider)`의 `DNS 서버`가 해당 도메인 주소를 호스팅하고 있는 IP 주소를 찾기 위해 `DNS 쿼리(DNS Query)`를 요청한다.

- DNS 쿼리의 목적: 해당 도메인의 IP 주소를 찾기 위함. IP 주소를 찾을 때까지 DNS 서버에서 다른 DNS 서버를 오가며 에러가 발생할 때까지 계속해서 반복해서 찾음 (`recursive search`)
- 이 때, ISP의 DNS 서버를 DNS recursor이라고 한다.

#### ③ 올바른 IP주소를 받게 되면 서버와 TCP connection을 한다

`TCP/IP 3 way handshake` 프로세스를 통해서 클라이언트와 서버 간 연결이 이루어진다.

#### ④ 브라우저가 웹 서버에 HTTP 요청을 보낸다

`GET` 요청으로 html 웹 페이지를 요구한다.

- 이 요청을 할 때, browser identification , accept header, connection header, cookie 등 부가적인 정보들이 함께 전달된다.

#### ⑤ 웹 서버(Web Server)가 먼저 해당 요청을 받아서 처리한다

웹 서버단에서 해결할 수 있는 `정적 컨텐츠(static contents)`일 경우 `WAS(Web Application Server)`에 요청할 필요 없이 일차적으로 WS가 처리한다.

#### ⑥ 웹 어플리케이션 서버(WAS)가 동적 컨텐츠 요청을 처리한다

웹 서버 단에서 처리할 수 없는 동적 페이지라면 웹 서버 뒷단에 위치한 WAS를 통해서 DB에서 필요한 데이터를 요청하고 응답받아 동적으로 페이지를 처리하고 웹 서버로 응답을 반환한다.

이후, 웹 서버는 요청 결과에 따른 http status 상태 코드와 함께  JSON, XML과 같은 포멧에 맞게 response를 생성한다.

#### ⑦ 웹 브라우저는 전달 받은 결과를 사용자에게 보여준다

웹 브라우저는 응답받은 웹 페이지 데이터를 렌더링 과정(브라우저 동작 과정)을 거쳐서 사용자에게 최종적으로 전달한다.