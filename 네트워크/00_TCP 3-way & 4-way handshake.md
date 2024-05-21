# 00_TCP 3way handshake & 4 way handshake

> 연결을 성립하고 해제하는 과정

- 3-way handshake: 연결 성립
- 4-way handshake: 연결 해제

아래에서 등장하는 SYN는 Synchronize, ACK는 Acknowledgment, FIN은 Finish를 의미한다.

- SYN (Synchronize): TCP 연결 설정을 시작하는데 사용되는 플래그로, 클라이언트가 서버로 연결 요청을 보낼 때 설정함
- ACK (Acknowledgment): 데이터의 성공적인 수신을 확인하는데 사용되는 플래그로,  SYN과 함께 사용되어 클라이언트가 서버의 응답을 확인하는데 사용됨
- FIN (Finish): TCP 연결 종료를 시작하는데 사용되는 플래그로, 클라이언트 또는 서버가 연결을 끊기를 원할 때 이 플래그를 설정함

<br>

## 1. 3-way handshake

> 연결을 성립(설정)하기 위해 진행하는 과정

TCP는 정확하고 신뢰성이 보장된 전송을 해야하기 때문에 아래와 같은 연결 과정을 거쳐서 논리적인 접속을 성립시킨다.

![image](https://github.com/siwon-park/cs-study-for-interview/assets/93081720/80f50281-fd5f-4da9-bc48-132c94e7d261)

### 1) 과정

총 3번의 통신 과정을 거친다고 해서 `3-way handshake`라고 부른다.

1. 클라이언트가 서버에게 `SYN` 패킷을 보냄 (sequence = x)
2. 서버는 SYN (sequence = x)을 받고, 클라이언트에게 받았다는 신호인 `ACK`와 함께 연결을 설정하기 위한 SYN를 보냄 (sequence = y, ack = x + 1)
   - x를 정상적으로 받았다는 의미해서 x의 다음 값인 x + 1을 보내는 것임
   - 그리고 송신자와 연결을 수립하기 위해서 새로운 연결 요청 신호인 y를 보내는 것임
3. 클라이언트는 서버의 응답 ACK (ack = x + 1)과 SYN (sequence = y)를 받고 ACK (ack = y + 1)을 보낸다

위와 같은 3번의 통신 과정을 성공적으로 거치게 되면 연결이 성립된다.

<br>

### 2) 상태

- CLOSED: 연결 수립 전의 상태. 연결 없음
- LISTEN: 포트가 열린 상태로 연결 요청 대기 중인 상태
- SYN-SENT: SYN를 보낸 상태
- SYN_RECEIVED: SYN 요청을 받고 상대방 응답을 기다리는 상태
- ESTABLISHED: 연결을 수립한 상태

<br>

## 2. 4-way handshake

> 연결을 해제하기 위해 진행하는 과정

연결을 성립하고 나서 모든 통신이 끝났다면, 연결을 해제해야 한다.

![image](https://github.com/siwon-park/cs-study-for-interview/assets/93081720/935b014e-673f-4487-b1e1-bb6e9ba593ac)

### 1) 과정

총 4번의 통신 과정을 거친다고 해서 `4-way handshake`라고 부른다.

1. 클라이언트는 서버에게 연결을 종료하겠다는 `FIN` 플래그를 보낸다. (sequence = x)
   - 그 후 FIN-WAIT-1 상태로 대기한다.
2. 서버는 FIN (sequence = x)를 받고 ACK를 클라이언트에게 보낸다.
   - 이 때, 서버는 모든 데이터를 내보내기 위해 `CLOSE WAIT` 상태가 된다.
3. ACK를 받은 클라이언트는 FIN-WAIT-2 상태로 대기한다.
4. 데이터를 모두 보냈다면, 서버는 연결이 종료되었다는 FIN 플래그를 클라이언트에 보낸다.
5. 클라이언트는 ACK와 FIN 플래그를 받고나서, 확인했다는 ACK를 서버에게 보낸다.
   - 아직 서버로 부터 받지 못한 데이터가 있을 수도 있으니 클라이언트는 `TIME WAIT` 상태로 기다린다.
6. 이후 서버는 클라이언트로 ACK를 최종적으로 받고나면 소켓을 닫는다. (통신 종료, CLOSED)
7. 클라이언트도 TIME WAIT 시간이 끝나면 통신을 닫는다 (CLOSED)

위와 같이 4번의 통신 과정을 성공적으로 거치게 되면 연결이 해제된다.

<br>

### 2) 상태

- ESTABLISHED: 연결이 수립되어 통신이 가능한 상태
- FIN-WAIT-1: 자신이 보낸 FIN에 대한 ACK를 기다리는 상태
- FIN-WAIT-2: 자신이 보낸 FIN에 대한 ACK는 받았고, 상대방의 FIN을 기다리는 상태
- CLOSE-WAIT: 상대방의 FIN을 받았고, 이 FIN에 대한 ACK를 보내고 어플리케이션에 종료를 알리는 상태
- LAST-ACK: ClOSE-WAIT 상태 처리 후 자신의 FIN 요청을 보내고 해당 FIN에 대한 ACK를 기다리는 상태
- TIME-WAIT: FIN과 ACK를 받았지만 아직 받지 못한 데이터를 위해 대기하는 상태
- CLOSED: 연결이 종료된 상태. (연결 없음)

<br