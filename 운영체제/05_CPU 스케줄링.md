# 05_CPU 스케줄링

- CPU 스케줄링은 왜 필요한가?
  - 여러 종류의 프로세스가 섞여있어 CPU와 I/O 장치 등 시스템 자원을 골고루 효율적으로 사용하기 위함
- CPU 스케줄링의 핵심
  - 공평보다 효율성, 사람이랑 더 많이 소통하는 프로그램에게 CPU를 더 많이 주기 위함

=> **★☆어떤 프로그램에게 CPU를 줄 것인가, 준 다음에는 계속 쓰게 할 것인가? 아니면 도중에 빼앗을 것인가?☆★**



### 01. 프로세스의 특성에 따른 분류

프로그램의 실행은 CPU burst와 I/O burst가 번갈아가며 일어난다

#### I/O bound process

짧은 CPU burst, I/O에 많은 시간이 필요한 job(사람과 상호작용하는 작업이 多, I/O bound job)

#### CPU bound process

 긴 CPU burst, 복잡한 계산 위주의 job(CPU bound job)



### 02. CPU 스케줄러와 Dispatcher

#### CPU 스케줄러

하드웨어나 소프트웨어가 아니라 운영체제 안에서 CPU 스케줄링 역할을 하는 코드가 있음

ready상태의 프로세스 중 이번에 CPU를 줄 프로세스를 고르는 역할을 함

#### Dispatcher

CPU 스케줄러에 의해 선택된 프로세스에게 CPU 제어권을 넘김(문맥 교환(Context Switch))



### 03. CPU 스케줄링이 필요한 경우

1. Running → Blocked (I/O 작업을 위해 CPU를 자진 반납)
2. Running → Ready (할당 시간 만료 후 time interrupt에 강제로 반납)
3. Blocked → Ready (I/O 작업 완료 후 인터럽트 당해서 강제로 반납)
   - 단, 항상 I/O작업이 끝난 이후 CPU를 바로 주는 것은 아님, 그러나 곧바로 줘야하는 경우도 有
4. Terminated (작업이 다 끝나고 자원과 CPU를 반납)



### 04. CPU 스케줄링 성능 척도(criteria)

- 시스템 입장에서의 성능 척도
  - CPU 이용률(CPU Utilization) : 전체 시간 중에 CPU가 일한 시간의 비율
  - 처리량(Throughput) : 주어진 시간 안에 얼마나 많은 일을 처리했는가
- 프로세스 입장에서의 성능 척도
  - 소요 시간(Turnaround time) : CPU를 쓰려고 들어와서 다 쓰고 나갈 때까지의 걸린 시간(기다리는 시간을 포함)
  - 대기 시간(Waiting time) : ready queue에서 기다린 시간
  - 응답 시간(Response time) : 최초로 CPU를 얻기까지 걸린 시간



### 05. CPU 스케줄링 알고리즘

#### - Non-Preemptive(비선점, 자발적으로 내놓음)

#### - Preemptive(선점, 강제로 빼앗음)

현대의 CPU는 대부분 Preemptive다

#### 01. FCFS(First Come First Serve)

선입 선출, Non-preemptive(비선점형)

CPU를 오래 쓰는 프로그램이 먼저 도착해서 수행되면 비효율적임 => Convoy Effect

(반대의 경우라면 상당히 효율적)



#### 02. SJF(Shortest Job First)

CPU burst time이 가장 짧은 프로세스를 먼저 스케줄링함 => minimum average waiting time 보장

- Non-preemptive : 더 짧은 프로세스가 큐에 도착하더라도 끝날 때까지 기다림, 해당 프로세스가 CPU를 다 쓰고 나면 새롭게 스케줄링함
- Preemptive : 더 짧은 프로세스가 도착하면 현재 프로세스에서 CPU를 빼앗아서 더 짧은 프로세스에게 줌(Shortest Remaining Time First; SRTF라고도 부름), CPU를 쓰면서 스케줄링함
- 문제점:
  - Starvation(기아 현상) : 긴 프로세스의 경우 영원히 CPU를 얻지 못할 수도 있음
  - 사실상 CPU 사용 시간을 미리 알 수 없음 => but 추정은 가능함(과거의 CPU burst time 이용)



#### 03. Priority Scheduling

우선순위가 가장 높은 프로세스에 CPU를 할당

- Non-preemptive : 더 높은 우선 순위를 가진 프로세스가 큐에 도착하더라도 끝날 때까지 기다림, 해당 프로세스가 CPU를 다 쓰고 나면 새롭게 스케줄링함
- Preemptive : 더 높은 우선 순위를 가진 프로세스가 도착하면 현재 프로세스에서 CPU를 빼앗아서 더 짧은 프로세스에게 줌. CPU를 쓰면서 스케줄링함

- SJF도 일종의 우선 순위 스케줄링임
- Starvation 발생 문제 => Aging 기법을 통해 해결
  - Aging 기법 : 오래 기다리는 프로세스의 우선 순위를 점차 높혀서 Starvation을 해결



#### 04. Round Robin

Preemptive, 현대CPU 스케줄링의 기반

각 프로세스는 동일한 크기의 할당 시간을 가짐 => 정해진 할당 시간이 지나면 Preempted(선점, 빼앗김) 당하고 ready queue의 제일 뒤에 가서 다시 줄을 선다

- CPU Response Time이 매우 빠름
- CPU를 빨리 쓰고 나가는 프로세스가 쓰고 나갈 수 있게 해줌

n개의 프로세스가 q time unit만큼 각 할당 시간을 가졌을 때, (n-1) * q time unit 이상으로 기다리지 않음

CPU를 길게 쓰는 프로세스는 waiting time이 길어지고, CPU를 짧게 쓰는 프로세스는 waiting time이 짧음

- 할당 시간을 크게 잡을 경우 => FCFS

- 할당 시간을 짧게 잡을 경우 => 문맥 교환(Context Switch)가 잦아 오버헤드가 커짐
- 일반적으로 SJF보다는 average turnaround time은 길지만, response time은 짧다