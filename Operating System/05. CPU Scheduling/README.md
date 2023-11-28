- [CPU Scheduling](#cpu-scheduling)
  - [프로세스의 특성 분류](#프로세스의-특성-분류)
  - [CPU Scheduler \& Dispatcher](#cpu-scheduler--dispatcher)

# CPU Scheduling

![Untitled](CPU%20Scheduling%200968c01ce3e54bb9bb08ee857d620217/Untitled.png)

- CPU burst : CPU를 사용하는 단계
- I/O burst : I/O를 사용하는 단계

→ 모든 프로그램은 이 둘이 반복적으로 실행된다.

![Untitled](CPU%20Scheduling%200968c01ce3e54bb9bb08ee857d620217/Untitled%201.png)

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
