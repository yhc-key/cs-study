- [CPU Scheduling](#cpu-scheduling)
  - [프로세스의 특성 분류](#프로세스의-특성-분류)
  - [CPU Scheduler \& Dispatcher](#cpu-scheduler--dispatcher)

# 📌 CPU Scheduling

![Untitled](CPU%20Scheduling/Untitled.png)

- CPU burst : CPU를 사용하는 단계
- I/O burst : I/O를 사용하는 단계

→ 모든 프로그램은 이 둘이 반복적으로 실행된다.

![Untitled](CPU%20Scheduling/Untitled%201.png)

- I/O bound job : CPU를 짧게 쓰고 I/O가 끼어드는 job(사람하고 interaction하는 프로그램) ⇒ 이때 CPU Scheduling 필요
- CPU bound job : I/O가 많이 끼어들지 않고 CPU를 계속 사용하는 job

## 프로세스의 특성 분류

- **I/O-bound process**
  - CPU를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 job
  - many short CPU bursts
- **CPU-bound process**
  - 계산 위주의 job
  - few very long CPU bursts

## CPU Scheduler & Dispatcher

- CPU Scheduler : Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다
- Dispatcher
  - CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘긴다
  - 이 과정을 context switch(문맥 교환)라고 한다.
- CPU 스케쥴링이 필요한 상태변화가 있는 경우
  1. **Running → Blocked**(ex: I/O 요청하는 시스템 콜)
  2. **Running → Ready**(ex: 할당시간만료로 timer interrupt)
  3. **Blocked → Ready**(ex: I/O완료 후 인터럽트→우선순위가 높은 애들은 이렇게 할 수 있음)
  4. **Terminate**
  > 1, 4에서의 스케줄링은 **nonpreemptive**(=자진 반납)
  > All other scheduling is **preemptive**(=강제로 빼앗음)

<br/>

# 📌 CPU Scheduling Algorithm
### 1. 비선점형 (non-preemptive)
- 일단 CPU 제어권을 줬으면 자진 반납할 때까지 강제로 빼앗지 않음

### 2. 선점형 (preemptive)
- Timer interrupt, Priority 등에 따라 강제로 빼앗을 수 있음
- 대부분 선점형 방식 사용

## CPU 성능 척도
![Untitled](CPU%20Scheduling/Untitled2.png)
1. **CPU utilization(이용률)** : 가능한 바쁘게 일을 시키는 것
2. **Throughput(처리량)** : 주어진 시간동안 몇개의 일을 처리/완료할 수 있는가
3. **Turnaround time(소요시간/반환시간)** : CPU를 쓰러 들어와서 다 쓰고 나가는 시간
4. **Waiting time(대기 시간)** : CPU를 쓰려고 기다리는 시간(순수하게 기다리는 시간)
5. **Response time(응답 시간)** : 처음으로 CPU를 얻기까지 걸리는 시간


- `시스템 입장`에서의 성능 척도
    - CPU 한 개로 최대한 일을 많이 시키면 좋음 → 이용률, 처리량
- `프로그램(클라이언트) 입장`에서의 성능 척도
    - CPU를 빨리 얻어 빨리 끝나면 좋음 → 소요시간, 반환시간, 대기 시간, 응답 시간



- 선점형은 얻었다 뺏겼다 하기 때문에 모든 대기시간을 합친 것이 `Waiting time`
- 기다리는 시간 + 쓰고 나가는 시간까지 전부 포함한 개념이 `Turnaround Time`

<br/>

# 📌 Scheduling Algorithm
## 1. FCFS(First-Come First-Served)

- 먼저 온 순서대로 처리
- `비선점형` 스케줄링
- 매우 효율적이진 않음 → 프로세스가 긴 프로그램이 끝날때까지 대기해야 함
- 즉, 앞에 어떤 프로그램이 대기하고 있는가에 따라 효율성 차이가 큼

![Untitled](CPU%20Scheduling/FCFS01.png)

![Untitled](CPU%20Scheduling/FCFS02.png)

## 2. SJF(Shortest-Job-First)
- 가장 짧은 프로세스부터 처리
- 평균 대기 시간의 **최소화** 를 보장함
- `비선점형`
  - 일단 CPU를 잡으면 더 짧은 프로세스가 와도 선점 당하지 않음
- `선점형`
  - 현재 수행중인 프로세스의 남은 시간보다 더 짧은 시간을 가지는 프로세스가 도착하면 CPU를 빼앗김
  - `Shortest-Remaining-Time-First(SRTF)` 라고도 함



- 비선점형 SJF

![Untitled](CPU%20Scheduling/SJF01.png)

- 선점형 SJF

![Untitled](CPU%20Scheduling/SJF02.png)

- 문제점
  - `Starvation` : 극단적으로 짧은 프로세스를 선호하므로, 프로세스가 긴 것은 계속 기다려야 함.
  - `CPU 사용 시간 예측 불가` → **추정만 가능**
    - 과거에 사용한 CPU 사용량을 가지고 추정하여 예측 값이 적은 것한테 부여

## 3. Priority Scheduling
- 각 프로세스마다 우선순위를 가짐
- 높은 우선순위를 가진 프로세스에게 CPU 할당
- SJF는 일종의 Priority scheduling
  - priority = predicted next CPU burst time
![Untitled](CPU%20Scheduling/PS01.png)

- 문제점
    - `Starvation` : 우선순위 낮은 프로세스는 영원히 CPU를 점유하지 못할 수도 있음
- 해결법
    - `Aging` : 오래 기다리면 프로세스의 우선순위를 높인다.
    

## 4. Round Robin(RR)

- 프로세스는 동일한 크기의 할당 시간을 가짐
- 할당 시간이 지나면 프로세스는 선점 당하고 ready queue 제일 뒤로 감
- 적당한 규모의 Time Quantum을 할당해야 함(10-100 milliseconds)

![Untitled](CPU%20Scheduling/RR01.png)

- 일반적으로 SJF보다 **평균 반환 시간**은 길지만 **응답 시간**은 짧다.

![Untitled](CPU%20Scheduling/RR02.png)

## 5. Multilevel Queue

- 우선순위가 `높은` 프로세스부터 처리
- 위에서 밑으로 갈 수록 우선순위 낮아짐

![Untitled](CPU%20Scheduling/MQ01.png)

- `foreground` : 우선순위 높은 큐 = 사용자와 상호작용
- `background` : 우선순위 낮은 큐 = 사용자와 직접적인 상호작용이 없음
- Time slice: Starvation을 막기 위해서 우선순위가 높은 큐에 80%, 우선순위 낮은 큐에 20% 할당

![Untitled](CPU%20Scheduling/MQ02.png)

## 6. Multilevel Feedback Queue

![Untitled](CPU%20Scheduling/MFQ01.png)

- 처음 들어오는 큐는 우선순위 높게 주지만 시간 짧게 줌
- 할당 시간 내에 처리가 안되면 할당 시간 더 받지만 우선순위 낮아짐
- 긴 프로세스는 점점 우선순위가 낮아지는 알고리즘

![Untitled](CPU%20Scheduling/MFQ02.png)

![Untitled](CPU%20Scheduling/MFQ03.png)

# 📌 CPU가 여러 개 있을 때 스케쥴링

→ 화두만 던지고 자세히 다루지는 않음

![Untitled](CPU%20Scheduling/MPS01.png)

### Real-Time Scheduling

- Hard real-time systems: 반드시 Deadline이 보장되어야 함
- Soft real-time computing : Deadline을 보장하진 못하지만 우선순위를 갖도록 해야 함

![Untitled](CPU%20Scheduling/RTS01.png)

### Thread Scheduling
- Global Scheduling : 운영체제는 Thread를 모르기 떄문에 그 프로세스에게 CPU를 줄 지 결정
- Local Scheduling : 그 프로세스에게 Thread가 갔을 떄 프로세스 내부에서 어떤 CPU에게 Thread를 줄건지 결정

![Untitled](CPU%20Scheduling/TS01.png)

## 어떤 알고리즘이 좋은지 평가하는 방법

- `Queueing models` : 도착률과 처리율이 주어져있을 떄 CPU 처리량 계산 가능
- `Implementation & Measurement` : 실제 시스템에 구현해서 돌려보고 성능 측정
- `Simulation` : 예제를 통해 계산하여 결과 비교

![Untitled](CPU%20Scheduling/AE01.png)
