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

![image](https://github.com/siwon-park/Problem_Solving/assets/93081720/0c472378-c787-4d20-9653-75ef24f09d18)

1. 클라이언트가 서버에게 `SYN` 패킷을 보냄 (sequence = x)
2. 서버는 SYN (sequence = x)을 받고, 클라이언트에게 받았다는 신호인 `ACK`와 함께 연결을 설정하기 위한 SYN를 보냄 (sequence = y, ack = x + 1)
3. 클라이언트는 서버의 응답 ACK (ack = x + 1)과 SYN (sequence = y)를 받고 ACK (ack = y + 1)을 보낸다

위와 같은 3번의 통신 과정을 성공적으로 거치게 되면 연결이 성립된다.

3번의 통신 과정을 거친다고 해서 `3 - way handshake`라고 부르는 것이다.

<br>

## 2. 4-way handshake

> 연결을 해제하기 위해 진행하는 과정

연결을 성립하고 나서 모든 통신이 끝났다면, 연결을 해제해야 한다.



![image](https://github.com/siwon-park/Problem_Solving/assets/93081720/b7ffd80c-5135-429f-8eac-8617b26780a3)

1. 클라이언트는 서버에게 연결을 종료하겠다는 FIN 플래그를 보낸다. (sequence = x)
2. 서버는 FIN (sequence = x)를 받고 ACK를 클라이언트에게 보낸다.
   - 이 때, 서버는 모든 데이터를 내보내기 위해 `CLOSE WAIT` 상태가 된다.
3. 데이터를 모두 보냈다면, 서버는 연결이 종료되었다는 FIN 플래그를 클라이언트에 보낸다.
4. 클라이언트는 ACK와 FIN 플래그를 받고나서, 확인했다는 ACK를 서버에게 보낸다.
   - 아직 서버로 부터 받지 못한 데이터가 있을 수도 있으니 클라이언트는 `TIME WAIT` 상태로 기다린다.
5. 이후 서버는 클라이언트로 ACK를 최종적으로 받고나면 소켓을 닫는다. (통신 종료, CLOSED)
6. 클라이언트도 TIME WAIT 시간이 끝나면 통신을 닫는다 (CLOSED)

위와 같이 4번의 통신 과정을 성공적으로 거치게 되면 연결이 해제된다.

4번의 통신 과정을 거친다고 해서 `4 - way handshake`라고 부른다.

<br>