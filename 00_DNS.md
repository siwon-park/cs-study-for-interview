# 00_DNS

> Domain Name System

웹 사이트의 IP 주소와 도메인을 맵핑하는 체계 혹은 시스템을 말한다.

우리가 특정 웹 사이트에 접속하기 위해서는 사실 해당 웹 사이트의 IP가 필요한데, 사용자가 일일이 IP 주소를 다 외우고 다닐 수도 없을 뿐더러, 해당 IP 주소가 어느 웹 사이트의 IP 주소인지 직접 들어가 보기 전까진 알 수 없다.

그래서 각 도메인 별로 맵핑된 IP 주소를 저장하고 있는 체계가 필요했고, 이를 DNS(Domain Name System)이라고 한다.

그리고 이 시스템에서 도메인 주소를 전달받았을 때, IP주소를 반환하는 서버를 `DNS 서버`라고 한다.

<br>

## 1. DNS 서버의 동작

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

<br>

### 2) Root DNS 서버

<br>

### 3) TLD DNS 서버

<br>

### 4) Authoritative DNS 서버