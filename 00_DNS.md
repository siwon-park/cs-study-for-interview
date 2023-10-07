# 00_DNS

> Domain Name System

웹 사이트의 IP 주소와 도메인을 맵핑하는 체계 혹은 시스템을 말한다.

우리가 특정 웹 사이트에 접속하기 위해서는 사실 해당 웹 사이트의 IP가 필요한데, 사용자가 일일이 IP 주소를 다 외우고 다닐 수도 없을 뿐더러, 해당 IP 주소가 어느 웹 사이트의 IP 주소인지 직접 들어가 보기 전까진 알 수 없다.

그래서 각 도메인 별로 맵핑된 IP 주소를 저장하고 있는 체계가 필요했고, 이를 DNS(Domain Name System)이라고 한다.

그리고 이 시스템에서 도메인 주소를 전달받았을 때, IP주소를 반환하는 서버를 `DNS 서버`라고 한다.

<br>

## 1. DNS 서버의 기본 동작 과정

위에서 언급했듯이 실제 웹 사이트의 데이터들이 저장된 주소는 IP 주소이다. 따라서 브라우저가 도메인을 검색하면, DNS서버에 맨 처음 해당 도메인에 대해 쿼리를 날려 찾고자 하는 웹 사이트의 IP 주소를 얻는다.

이렇게 얻은 IP 주소를 호스팅 서버에 요청하여 웹 사이트의 데이터를 사용자에게 제공하는 것이다.

<img src="https://github.com/siwon-park/cs-study-for-interview/assets/93081720/ada83ae6-71ea-4b95-9f62-4636be8cbbf3" referrerpolicy="no-referrer" alt="image" height="500px">

그러나 위의 그림은 매우 간략한 DNS 서버 동작을 표현한 것이고, 실제로는 DNS 서버가 여러 계층으로 나눠져서 동작한다.

<br>

## 2. DNS 서버의 종류

DNS 서버는 크게

- Recursive DNS (ISP(인터넷 서비스 제공자)의 DNS 서버, Local DNS 서버)
- Root DNS (루트 DNS 서버)
- TLD(Top Level Domain) DNS (최상위 DNS 서버)
- Authoritative DNS

로 나뉜다.

### 1) Recursive DNS 서버

인터넷 사용자가 가장 먼저 접근하게 되는 DNS 서버.

매번 Root DNS 서버, TLD DNS 서버, Authoritative DNS 서버를 거쳐 IP 주소를 얻어낸다면 비효율적일 수밖에 없으니, 한 번 거친 후 얻은 데이터를 캐싱해두었다가 동일한 요청이 들어왔을 때 캐싱된 데이터를 사용한다.

가장 대표적인 게 ISP(인터넷 서비스 제공자) DNS 서버이다.

<br>

### 2) Root DNS 서버

ICANN이 직접 관리하는 DNS 서버로, TLD DNS 서버의 IP 주소들을 저장해두고 안내하는 역할을 한다.

<br>

### 3) TLD DNS 서버

도메인 등록 기관(Registry)이 관리하는 DNS 서버로, Authoritative DNS 서버의 IP 주소들을 저장해두고 안내하는 역할을 한다.

어떤 도메인 묶음이 어떤 Authoritative DNS 서버에 속하는지 알 수 있는 이유는 도메인 판매 업체(Register)의 DNS 설정이 변경되면 도메인 등록 기관으로 전달되기 때문에 알 수 있는 것임.

<br>

### 4) Authoritative DNS 서버

도메인 판매 업체(Register)가 관리하는 DNS 서버로, 실제 개별 도메인과 IP 주소의 관계가 기록/저장/변경되는 서버이다. 그래서 권한이라는 의미인 Authoritative가 붙는 것이다.

실제 네임 서버를 설정하는 곳으로, 일반적으로는 도메인/호스팅 업체의 네임 서버를 말하지만, 개인 DNS 서버를 구축한 경우에도 Authoritative DNS 서버에 포함된다.

<br>

## 3. 전체적인 DNS 서버의 동작 과정

계층별로 DNS 서버를 따라가다가 Authoritative DNS 서버에서 최종적으로 찾고자 하는 IP주소를 찾는 것임

![image](https://github.com/siwon-park/cs-study-for-interview/assets/93081720/b21aaa65-8f89-4692-a1ff-ae4e1cf61c01)

1. 브라우저에서 www.AOO.com을 검색하고, 사용하고 있는 ISP의 DNS 서버에 도메인 주소에 해당하는 IP 주소를 요청함.
2. ISP의 DNS 서버는 일단 해당 도메인이 캐시된 데이터에 있는지 확인하고, 캐시된 데이터에 있으면 IP 주소를 반환하고, 없다면 Root DNS 서버에게 어디로 가야하는지 요청함.
3. Root DNS 서버는 TLD DNS 서버 주소만 관리하기 때문에 요청한 도메인에서 `.com`을 보고 COM 최상위 도메인을 관리하는 TLD DNS 서버로 갈 수 있도록 주소를 안내함.
4. ISP의 DNS 서버는 이번에는 COM 서버에게 어디로 가야하는지 요청하고, COM 서버는 해당 도메인이 관리되고 있는 Authoritative DNS 서버를 확인하고 주소를 반환함.
5. ISP의 DNS 서버는 TLD DNS 서버로부터 반환받은 Authoritative DNS 서버로 요청하여 제일 처음 브라우저가 요청했던 도메인 주소에 해당하는 IP 주소를 알아냄.
6. ISP의 DNS 서버는 최종적으로 얻은 IP 주소를 브라우저에게 반환하고, 이를 캐싱해두었다가 나중에 동일한 요청이 들어올 때 캐싱된 데이터를 사용함.
7. 브라우저는 ISP의 DNS 서버로부터 얻은 IP 주소를 통해 해당 IP 주소를 호스팅하고 있는 호스팅 서버에 데이터를 요청함.
8. 웹 페이지가 보임.

