# 07_데드락(Deadlock)

데드락; 교착 상태

## 01. Deadlock problem

일련의 프로세스들이 서로가 가진 자원을 기다리며 blocked된 상태

- 자원
  - 하드웨어, 소프트웨어 등을 포함하는 개념
    - 예) I/O device, memory space, semaphore 등
  - 프로세스가 자원을 사용하는 과정: Request -> Allocate -> Use -> Release
- Deadlock example
  - Binary semaphores A and B
    - 프로세스(p0)과 프로세스(p1)은 교착 상태에 있음
    - P0은 A를 얻고 B를 얻으려 하지만 P1은 B를 얻고 A를 얻으려고함. 그런데 서로 각자 가지고 있는 자원을 내려놓기 전에는 원하는 자원을 획득할 수 없음

![image](https://user-images.githubusercontent.com/93081720/166127558-98abc888-0e4e-45f9-b546-986024ca12fb.png)

<br>

## 02. Deadlock 발생 조건

### 1. Mutual Exclusion(mutex, 상호 배제)

매 순간 하나의 프로세스만이 자원을 사용할 수 있음

<br>

### 2. Non Preemption(비선점)

프로세스는 자원을 스스로 내어놓을 뿐 강제로 빼앗기지 않음

<br>

### 3. Hold and Wait(보유 대기)

자원을 가진 프로세스가 다른 자원을 기다릴 때 현재 보유한 자원을 놓지 않고 계속 가지고 있음(기다린 자원을 얻은 다음 갖고 있는 자원을 내려 놓음)

<br>

### 4. Cicular wait(순환대기)

자원을 기다리는 프로세스 간에 사이클이 형성되어야 함

- 예) 프로세스 P0, P1, P2, ... , Pn이 있을 때
  - P0은 P1이 가진 자원을 기다리고, P1은 P2가 가진 자원을 기다리고, P2는 P3가 가진 자원을 기다리며, Pn은 P0이 가진 자원을 기다림

<br>

=> 강제로 빼앗긴다면 데드락은 발생하지 않음

<br>

----

## 03. Resoucre-Allocation Graph

그래프에 사이클이 없으면 Deadlock이 아니다

그렇다고 사이클이 있다고 해서 무조건 deadlock은 아님

- 자원당 인스턴스가 하나밖에 없으면(자원 타입에 따른 자원의 수가 1개라면) => 사이클이 있으면 교착 상태
- 자원당 인스턴스가 여러 개라면(자원 타입에 따른 자원 수 가 여러 개라면) => 사이클이 있더라도 교착 상태 가능성이 있음(자세히 따져 봐야함)

![image](https://user-images.githubusercontent.com/93081720/166127917-32c7e397-fcea-461d-b11c-1bddc98964ce.png)

위 첫번째 그림에서 자원 R2의 인스턴스는 2개이지만 교착상태에 놓여있음

**두번째 그림은 사이클이 발생하긴 했지만 교착 상태가 아님** => R2에 대해서 P4가 쓰고나서 반납하기만 하면 교착상태는 발생하지 않음(또는 R1에 대해서 P2가 쓰고나서 반납하기만 하면 교착 상태가 풀림) 

<br>

----

## 04. DeadLock의 처리 방법

### 1. Deadlock Prevention

자원 할당 시 Deadlock의 4가지 필요 조건 중 어느 하나가 만족되지 않도록 하는 것

- Mutual Exclusion(mutex, 상호 배제)
  - 공유해서는 안 되는 자원의 경우 해당 조건은 반드시 성립해야함

- Non Preemption(비선점)

  - 자원을 빼앗을 수 있게 함 => 예) CPU, Memory 등

    - 상태를 쉽게 save하고 restore할 수 있는 자원에 대해 주로 사용

    - 프로세스가 어떤 자원을 기다려야 하는 경우 이미 보유한 자원이 선점됨
    - 모든 필요한 자원을 얻을 수 있을 때 그 프로세스는 다시 시작됨

- Hold and Wait(보유 대기)
  - 프로세스가 자원을 요청할 때 다른 어떠한 자원도 가지고 있지 않아야 한다
    - 방법1. 프로세스가 시작 시 모든 필요한 자원을 할당받게 하는 방법 => 처음부터 다 받고 다 쓰고 나면 반납 => 매시점마다 필요한 자원이 다른데 이렇게 하면 자원에 대한 비효율이 발생함
    - 방법2. 자원이 필요할 경우 보유한 자원을 모두 내려놓고 다시 요청함(자진 반납)

- Cicular wait(순환대기)
  - 모든 자원 유형에 할당 순서를 정해서 정해진 순서대로만 자원을 할당함

=> 생기지도 않을 deadlock에 대해 사전에 방지하는 방법이므로 저원에 대한 활용 저하(Utilization 저하), 생산성 감소(Throughput 감소), Starvation 문제 등 비효율이 발생한다

<br>

### 2. Deadlock Avoidance

자원 요청에 대한 부가적인 정보를 이용해서 deadlock의 가능성이 없는 경우에만 자원을 할당함

시스템 상태가 원래 상태로 되돌아올 수 있는 경우에만 자원을 할당

최악의 상황을 가정하여 safe state인지 조사함

- safe state : 시스템 내의 프로세스들에 대한 safe sequence가 존재하는 상태
- safe sequence : 프로세스의 시퀀스(P1, P2, P3, ..., Pn)가 safe하려면 Pi(1 <= i <= n)의 자원요청이 '가용 자원 + 모든 Pj(j <i)의 보유 자원'에 의해 충족되어야 한다.

<br>

- 자원이 싱글 인스턴스일 경우 => 자원 할당 그래프를 활용하여 safe state에 대해 조사함
- 자원이 멀티 인스턴스일 경우 => Banker's Algorithm 
  - 현재 가용 자원으로 어떤 한 프로세스가 최대로 요청할 수 있는 자원양을 충족할 수 있는가? => 최대 요청 자원을 충족할 수 있다면 safe state이므로 자원을 할당해줌
  - 현재 가용 자원으로 어떤 프로세스의 최대 요청 자원양을 충족시키지 못한다면 가용한 자원이 남아 있더라도 unsafe한 state가 발생할 수 있기 때문에 자원을 주지 않음

![image](https://user-images.githubusercontent.com/93081720/166150747-ff568390-4560-4584-bf9d-35609c5db6a0.png)

- P0이 자원을 요청하면 자원을 주지 않지만, P1이 자원을 요청하면 자원을 줌

- 프로세스의 시퀀스에 따라 자원의 요청/할당에 대한 시스템 safe state가 가능함
  - P1이 요청하고 반납 (5, 3 2) -> P3가 요청하고 반납 (7, 4, 3) -> P0이 요청해도 자원을 줄 수 있음(또는 다른 프로세스의 자원 요청을 수락)
- 자원을 줄 수 있는 상황임에도 최악의 상황을 가정해서 자원을 주지 않으므로 비효율적임
  - 가용 자원만으로 프로세스의 자원요청을 충족할 수 없음에도 자원을 주는 것은 교착 상태가 아님
    - unsafe상태라고 해서 무조건 deadlock은 아니라는 의미임

<br>

### 3. Deadlock Detection and Recovery

deadlock의 발생은 허용하되, 그에 대한 detection 루틴을 두어 deadlock 발견 시 recover함

#### Deadlock Detection

- 자원 타입 당 싱글 인스턴스인 경우
  - 자원할당 그래프에서의 사이클이 곧 deadlock을 의미
  - 사이클을 찾는데는 O(n^2)의 시간복잡도가 걸림
- 자원 타입 당 멀티 인스턴스인 경우
  - 뱅커스 알고리즘과 유사한 방법 활용

![image](https://user-images.githubusercontent.com/93081720/166151925-0e3f0b1d-3dd5-4b7a-96dc-75823a487712.png)

- 아무것도 요청하지 않은 프로세스의 할당된 자원이 반납되면 자원 할당이 가능
  - 그런데 만약 여기서 P2가 C를 1개 요청한다면 deadlock발생

#### Deadlock Recovery

deadlock이 발견되면 recovery를 시행

- Process Termination
  - 방법1) deadlock에 연루된 프로세스를 전부 죽이는 방법
  - 방법2) deadlock에 연루된 프로세스를 하나씩 죽여나가서 deadlock을 제거함
-  Resouce Preemption
  - 프로세스 중에서 희생양을 찾아서 자원을 빼앗아 deadlock을 제거함
  - safe state로 rollback하여 프로세스를 restart
  - 그러나 starvation문제 발생 가능성 존재
    - 동일한 특정 프로세스만 계속해서 희생양으로 선정되는 경우
    - rollback횟수도 고려하면 해결 가능

<br>

### 4. Deadlock Ignorance

현재의 운영체제 대부분이 채택하고 있음 => Deadlock을 시스템이 책임지지 않음. 시스템이 안 돌아가면 사용자가 시스템을 끄고 다시 재실행함(사용자에게 맡김)

Deadlock이 일어나지 않는다고 생각하고 아무런 조치도 취하지 않음

- 이유? 
  - Deadlock 자체가 매우 드물게 발생하므로 이에 대한 미연의 방지 조치를 미리 하는 것이 오히려 더 큰 오버헤드를 발생시킬 수 있음
- 그렇다면 deadlock 발생 시 어떻게 해결?
  - deadlock이 발생하여 시스템이 비정상적으로 작동하는 것을 사람이 직접 프로세스를 죽이는 방식으로 대처

