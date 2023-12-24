# 06_CPU 스케줄링 (프로세스 스케줄링)

> ★☆어떤 프로그램에게 CPU를 줄 것인가, 준 다음에는 계속 쓰게 할 것인가? 아니면 도중에 빼앗을 것인가?☆★

### 1) CPU 스케줄링은 왜 필요한가?

- 여러 종류의 프로세스가 섞여있어 CPU와 I/O 장치 등 `시스템 자원을 골고루 효율적으로 사용하기 위함`
- 사람과 상호작용 하는 일과 CPU를 많이 쓰는 일 등 다양한 작업이 섞여있고 이들의 CPU 사용 시간이 다르기 때문에 이를 조율하기 위해서 CPU 스케줄링이 필요함

### 2) CPU 스케줄링의 핵심

- 공평보다 `효율성`, `사람이랑 더 많이 소통하는 프로그램에게 CPU를 더 많이 주기 위함`
  - 적절한 프로세스에게 자원을 쓸 수 있게 해주기 위함


<br>

## 1. CPU 스케줄링 개요

### 1) 프로세스의 특성에 따른 분류

프로그램의 실행은 `I/O burst`와 `CPU burst`가 번갈아가며 일어난다.

이처럼 작업에 따라 CPU를 많이 사용하는 작업이 있는가하면, 조금만 사용하는 작업도 있기 때문에 조율이 필요하다.

#### (1) I/O bound process

짧은 CPU burst, I/O에 많은 시간이 필요한 Job(사람과 상호작용하는 작업이 多, I/O bound Job)

#### (2) CPU bound process

 긴 CPU burst, 복잡한 계산 위주의 Job(CPU bound Job)

<br>

### 2) CPU 스케줄러와 디스패처(Dispatcher)

#### (1) CPU 스케줄러

하드웨어나 소프트웨어가 아니라 운영체제 안에서 CPU 스케줄링 역할을 하는 코드가 있음

Ready상태의 프로세스 중 이번에 CPU를 줄 프로세스를 고르는 역할을 함

#### (2) Dispatcher

CPU 스케줄러에 의해 선택된 프로세스에게 CPU 제어권을 넘김 (`문맥 교환, Context Switching`)

<br>

### 3) CPU 스케줄링이 필요한 경우

프로세스가 다음과 같은 상태일 때 CPU 스케줄링이 필요하다.

1. Running → Blocked(Waiting) (I/O 작업을 위해 CPU를 자진 반납)
2. Running → Ready (할당 시간 만료 후 time interrupt에 강제로 반납, time out)
3. Blocked(Waiting) → Ready (I/O 작업 완료 후 인터럽트 당해서 강제로 반납)
   - 단, 항상 I/O작업이 끝난 이후 CPU를 바로 주는 것은 아님, 그러나 곧바로 줘야하는 경우도 有
4. Terminated (작업이 다 끝나고 자원과 CPU를 반납)

<br>

### 4) CPU 스케줄링 성능 척도(criteria)

- 시스템 입장에서의 성능 척도
  - CPU 이용률(CPU Utilization) : 전체 시간 중에 CPU가 일한 시간의 비율
  - 처리량(Throughput) : 주어진 시간 안에 얼마나 많은 일을 처리했는가
- 프로세스 입장에서의 성능 척도
  - 소요 시간(Turnaround time) : CPU를 쓰려고 들어와서 다 쓰고 나갈 때까지의 걸린 시간(기다리는 시간을 포함)
  - 대기 시간(Waiting time) : ready queue에서 기다린 시간
  - 응답 시간(Response time) : 최초로 CPU를 얻기까지 걸린 시간

<br>

## 05. CPU 스케줄링 알고리즘

CPU 스케줄링 알고리즘은 크게 `Preemptive(선점)`, `Non-Preemptive(비선점)`으로 나눌 수 있다.

![image](https://github.com/siwon-park/cs-study-for-interview/assets/93081720/4cab2218-1bae-41b1-9fe0-eec4d1509838)

### 1) Preemptive (선점, 강제로 빼앗음)

> 하나의 프로세스가 CPU를 차지하고 있을 때, 우선 순위가 높은 다른 프로세스가 현재 실행 중인 프로세스를 중단시키고, CPU를 점유하는 스케줄링 방식

현대의 CPU는 대부분 Preemptive 방식으로 스케줄링을 한다.

<br>

#### (1) Round Robin (라운드 로빈)

>  현대 CPU 스케줄링의 기반

각 프로세스는 동일한 크기의 할당 시간을 가지고, 정해진 할당 시간이 지나면 사용 중이던 CPU를 강제로 빼앗기고 준비 큐(ready queue)의 제일 뒤에 가서 다시 줄을 선다.

- 특징 

  - CPU Response Time이 매우 빠름

  - CPU를 빨리 쓰고 나가는 프로세스가 먼저 CPU를 쓰고 나갈 수 있게 해줌

  - 효율적이고 공평한 CPU 스케줄링 기법


`n`개의 프로세스가 `q` time unit만큼 각 할당 시간을 가졌을 때, `(n-1) * q` time unit 이상으로 기다리지 않음

CPU를 길게 쓰는 프로세스는 waiting time이 길어지고, CPU를 짧게 쓰는 프로세스는 waiting time이 짧음

- 할당 시간을 크게 잡을 경우 => FCFS
- 할당 시간을 짧게 잡을 경우 => 문맥 교환(Context Switch)가 잦아 오버헤드가 커짐
- 일반적으로 SJF보다는 average turnaround time은 길지만, response time은 짧다

<br>

#### (2) SRTF (Shortest Remaining Time First, 최소 잔여 우선)

자원 사용의 남은 시간이 더 짧은 프로세스가 도착하면 현재 프로세스에서 CPU를 빼앗아서 더 짧은 프로세스에게 줌. SRT라고도 함.

SJF 방식과 유사하나, SRTF는 선점이고 SJF는 비선점이라는 점에서 차이가 있음.

<br>

#### (3) Multilevel queue (다단계 큐)

다계층 다중 큐 방식. 각 큐별로 고정된 우선 순위가 있고, 독립적인 스케줄링 기법을 갖고 있음

"어떤 큐에 CPU를 줄 것인가, 큐 안에서 누구에게 먼저 CPU를 줄 것인가?"가 구현의 다단계 큐 스케줄링 구현의 핵심임.

- 구현
  - ready queue를 여러 개로 분할
    - foreground(interactive) / round robin
    - background(batch; non-human interactive) / fcfs
  - queue에 대한 스케줄링
    - Fixed priority scheduling => f.g에 먼저 cpu를 주고, 그 다음에 b.g에 줌 -> starvation 가능성
    - Time slice => 각 큐에 CPU time을 적절한 비율로 할당

<br>

#### (4) Multilevel feedback queue (다단계 피드백 큐)

Multilevel queue와 비슷하지만, 프로세스가 다른 큐로 이동 가능함

구현을 위해 "큐를 몇 개 둘 것인가?", "각 큐의 알고리즘은 어떤 것을 사용할 것인가?", "우선 순위가 높은 큐에서 낮은 큐로 옮겨지게 되는 기준은?", "처음 프로세스는 어느 큐에 들어갈 것인가?"에 대한 결정이 필요함.

처음 프로세스는 우선순위가 가장 높은 큐로 할당되지만, 주어진 시간 안에 일을 못 끝냈으면, 다음 우선순위를 가진 큐로 가서 대기함 (프로세스가 다른 큐로 이동)

<br>

### 2) Non-Preemptive (비선점, 자발적으로 내놓음)

> 하나의 프로세스가 CPU를 할당 받으면 작업 종료 및 CPU 반환 전까지 다른 프로세스는 CPU 점유가 불가능한 스케줄링 방식

#### (1) FCFS(First Come First Serve, 선입 선출)

먼저 도착하는 작업이 CPU를 점유하여 작업을 수행하며, 다음 프로세스는 이전 프로세스가 종료될 때까지 기다림.

CPU를 짧게 쓰는 작업이 계속해서 먼저 도착한다면 상당히 효율적이겠지만,

CPU를 오래 쓰는 프로그램이 먼저 도착해서 수행되면 비효율적임(`Convoy Effect(호위 효과)`)

<br>

#### (2) SJF(Shortest Job First, 최단 작업 우선)

CPU burst time이 가장 짧은 프로세스를 먼저 스케줄링함

- Minimum Average Waiting Time (최소 평균 대기 시간) 보장

더 짧은 프로세스가 큐에 도착하더라도 이미 수행 중인 작업이 끝날 때까지 기다리고,  작업 중인 프로세스가 CPU를 다 쓰고 나면 새롭게 스케줄링 하여, 최단 작업 시간을 가지는 프로세스가 먼저 CPU를 점유함.

- 문제점:
  - `Starvation(기아 현상)` : 매번 현재 시점에 작업 시간이 짧은 프로세스를 우선적으로 스케줄링을 실시하므로, 작업 시간이 긴 프로세스의 경우 영원히 CPU를 얻지 못할 수도 있음
  - 정확한 CPU 사용 시간을 미리 알 수 없어 사실상 스케줄링을 구현할 수 없음 (but 과거의 CPU burst time 이용해 추정은 가능함)

<br>

#### (3) Priority Scheduling (우선순위 스케줄링)

각 프로세스별로 우선순위가 주어지고, 우선순위가 가장 높은 프로세스에 CPU를 먼저 할당함. 우선순위 스케줄링의 경우 비선점형과 선점형 둘 다 존재함.

- Non-preemptive : 더 높은 우선 순위를 가진 프로세스가 큐에 도착하더라도 끝날 때까지 기다림, 해당 프로세스가 CPU를 다 쓰고 나면 새롭게 스케줄링함
  - SJF도 일종의 우선 순위 스케줄링임

- Preemptive : 더 높은 우선 순위를 가진 프로세스가 도착하면 현재 프로세스에서 CPU를 빼앗아서 더 짧은 프로세스에게 줌. CPU를 쓰면서 스케줄링함

우선순위에 따라 스케줄링하니 Starvation 문제 발생 => Aging 기법을 통해 해결
- Aging 기법 : 오래 기다리는 프로세스의 우선 순위를 점차 높혀서 프로세스가 무한 대기하는 현상을 제거함.

<br>

#### (4) Realtime Scheduling (실시간 스케줄링)

Deadline이 보장되어야 하는 중요한 일에 대한 Scheduling

- Hard real time scheduling : 반드시 정해진 시간 안에 끝내도록 스케줄링
- Soft real time scheduling : 데드라인을 반드시 지켜야하진 않지만 일반 프로세스에 비해 높은 우선순위 할당

<br>

### 3) 기타 스케줄링

#### (1) MultiProcessor Scheduling

다중 CPU에 대한 스케줄링, CPU가 여러 개인경우 스케줄링은 더욱 복잡해짐

- Homogeneous Processor인 경우
  - 큐에 프로세스를 한 줄로 세워서 각 프로세서가 알아서 꺼내가게 할 수 있음
  - 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우에는 더 복잡해짐
- Load Sharing
  - 일부 프로세서에만 일이 몰리지 않도록 부하(load)를 적절히 공유하는 메커니즘 필요
    - 별개의 큐를 두는 방법 vs. 공동 큐를 두는 방법
- Symmetric MultiProcessing : 각 프로세서가 각자 알아서 스케줄링
- Asymmetric MultiProcessing : 하나의 프로세스가 시스템 데이터의 접근과 공유를 책임지고, 나머지는 그에 따르는 방식

<br>

#### (2) Thread Scheduling

- Local scheduling : user level thread의 경우, OS가 아니라 사용자 프로세스가 직접 CPU를 어느 thread에 줄 것인지 결정
- Global scheduling : kernel level thread의 경우, 일반 프로세스와 마찬가지로 커널의 단기 스케줄러가 thread 스케줄링을 결정

<br>

### 4) 알고리즘 평가(Algorithm Evaluation)

어떤 알고리즘이 더 좋은가?

- Queueing Model : 이론적인 값을 통해 Performance Index를 측정
- Implementation(구현) & Measurement(측정) : 실제 시스템에 알고리즘을 구현하여 실작업에 대한 성능 측정
- Simulation(시뮬레이션) : trace(시뮬레이션에 들어갈 인풋 데이터)를 가지고 시뮬레이션하여 결과를 비교

