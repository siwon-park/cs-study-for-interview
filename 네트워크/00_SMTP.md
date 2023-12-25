# 00_SMTP

> Simple Mail Transfer Protocol

SMTP는 전자 우편을 송수신하는데 사용되는 TCP/IP 프로토콜이다. 간단하게 말해 전자 메일을 위한 표준 프로토콜이라고 보면 된다.

일반적으로 `POP3`, `IMAP(Internet Message Access Protocol)`과 함께 사용되어 메세지를 서버 메일함에 저장하고 사용자를 위해서 주기적으로 메세지를 다운받는다.

<br>

## 1. 특징

SMTP의 대표적인 특징은 다음과 같다.

- 텍스트 기반 프로토콜
- TCP/IP 기반 프로토콜
  - 따라서 SMTP 서버로의 접근 가능성 확인, 서버와 연결 설정, 메일 전송이라는 단계를 순차적으로 거친다.
- SMTP 서버와 클라이언트로 구성

<br>

### 1) SMTP 서버와 클라이언트

#### (1) SMTP 클라이언트

메일을 보낸 주체

※ 메일을 받는 상대방쪽은 클라이언트가 아니다.

- 왜냐하면 결국 SMTP 클라이언트가 SMTP 서버로 데이터를 보내고 받는 쪽은 해당 서버로부터 메세지를 수신만 받기 때문이다.

![image](https://github.com/siwon-park/cs-study-for-interview/assets/93081720/50af493e-1eae-4e6c-85c8-6ddc0654069c)

#### (2) SMTP 서버

메일 데이터를 송수신하는 서버

예를 들어, gmail의 SMTP 서버는 smtp.gmail.com이고, naver의 SMTP 서버는 smtp.naver.com이다.

결국 클라이언트가 본인의 메일 주소로 메일을 보내면 해당 데이터는 사용하는 메일 도메인의 메일 서버로 1차적으로 보내진 다음에, 수신측의 메일 서버로 보내지는 것이다.

- 예) gmail → naver로 메일 송신; gmail SMTP서버 → naver SMTP서버

<br>

## 2. 동작 과정

송신자가 메일을 작성해서 보내면 해당 메일이 SMTP 서버(보내는 메일 서버, Outgoing Mail Server)로 전송되며, 메일을 받은 SMTP 서버는 SENDMAIL 프로그램을 구동하여 해당 메일을 알맞은 수신자에게 보내는 것이다.

### 1) DNS와 MX 레코드

MX 레코드란 메일이 수신될 위치를 결정하는 레코드를 말한다.

(동작 예시)

1. mydomain.kr의 MX 레코드를 네이버웍스의 MX 레코드 값으로 설정
2. 외부에서 id@mydomain.kr로 메일을 보내는 경우
3. DNS에 네이버웍스의 MX 레코드가 조회되므로 네이버웍스 서버로 메일을 전송
4. 네이버웍스의 메일함에서 수신된 메일 확인