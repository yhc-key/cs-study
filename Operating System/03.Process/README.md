# Process

- [Process](#process)
- [Process](#process-1)
  - [Process State](#process-state)
  - [Process Control Block(PCB)](#process-control-blockpcb)
  - [Context Switch(문맥 교환)](#context-switch문맥-교환)
  - [프로세스를 스케쥴링하기 위한 큐](#프로세스를-스케쥴링하기-위한-큐)
  - [Scheduler](#scheduler)
  - [Thread](#thread)
    - [Thread의 장점](#thread의-장점)
    - [Implementation of Thread(구현 방식)](#implementation-of-thread구현-방식)

# Process

Process is a program in execution.

![Untitled](Process%20781ff64f806e463b9001b9ceaaf51656/Untitled.png)

- 프로세스의 문맥

: 특정 시점에 봤을 때 프로세스가 어디까지 수행을 했는지

- CPU 수행 상태를 나타내는 하드웨어 문맥
  - Program Counter가 어떤 값을 가지고 있었나
  - 각종 register
- 프로세스의 주소 공간
  - code, data, stack
- 프로세스 관련 커널 자료 구조
  - PCB(Process Control Block)
  - Kernel stack

## Process State

프로세스는 상태가 변경되며 수행된다. 기본적으로는 위의 3가지 상태 중 하나임.

- **Running** : CPU를 잡고 Instruction을 수행중인 상태
- **Ready** : CPU를 기다리는 상태 (메모리 등 다른 조건을 모두 만족하고)
- **Blocked**(wait, sleep)
  - CPU를 주어도 당장 instruction을 수행할 수 없는 상태
  - Process 자신이 요청한 event(예:I/O)가 즉시 만족되지 않아 이를 기다리는 상태
  - ex) 디스크에서 file을 읽어와야 하는 경우
- **Suspended**(stopped)

  - 외부적인 이유로 프로세스의 수행이 정지된 상태
  - 프로세스는 통째로 디스크에 swap out 된다
  - Mid-term scheduler 담당
  - ex) 사용자가 프로그램을 일시 정지시킨 경우(break key) → 시스템이 여러 이유로 프로세스를 잠시 중단시킴(메모리에 너무 많은 프로세스가 올라와 있을 때)

  | Blocked                              | Suspended                       |
  | ------------------------------------ | ------------------------------- |
  | 자신이 요청한 event가 만족되면 Ready | 외부에서 resume해 주어야 Active |

- New : 프로세스가 생성 중인 상태
- Terminated : execution(수행)이 끝난 상태

![Untitled](Process%20781ff64f806e463b9001b9ceaaf51656/Untitled%201.png)

![Untitled](Process%20781ff64f806e463b9001b9ceaaf51656/Untitled%202.png)

![Untitled](Process%20781ff64f806e463b9001b9ceaaf51656/Untitled%203.png)

Running이 user, monitor로 나뉘어도 내부에서는 둘 다 running이라고 생각함

우리가 상태를 running, blocked, ready로 나눴어도 내부에서는 그렇게 나누지 않음.

→운영체제가 running을 하고 있다(x) | 사용자 프로세스가 커널 모드에서 running하고 있다(O)

## Process Control Block(PCB)

운영체제가 각 프로세스를 관리하기 위해 프로세스 당 유지하는 정보

다음의 구성 요소를 가짐(구조체로 유지)

1. OS가 관리상 사용하는 정보
   - Process state, Process ID
   - Scheduling information, priority
2. CPU 수행 관련 하드웨어 값
   - Program counter, registers
3. 메모리 관련
   - Code, Data, Stack의 위치 정보
4. 파일 관련
   - Open file descriptors

![Untitled](Process%20781ff64f806e463b9001b9ceaaf51656/Untitled%204.png)

## Context Switch(문맥 교환)

CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정

CPU가 다른 프로세스에게 넘어갈 때 운영체제는 다음을 수행

- CPU를 내어주는 프로세스의 상태를 그 프로세스의 PCB에 저장
- CPU를 새롭게 얻는 프로세스의 상태를 PCB에서 읽어옴

![Untitled](Process%20781ff64f806e463b9001b9ceaaf51656/Untitled%205.png)

![여기 나오는 저 data 부분에 PCB가 저장되는 것임!](Process%20781ff64f806e463b9001b9ceaaf51656/Untitled.png)

여기 나오는 저 data 부분에 PCB가 저장되는 것임!

- **System call이나 Interrupt 발생시 반드시 context switch가 일어나는 것은 아님**
  - 사용자 프로세스 간에 전환이 일어날 때 context switch가 일어남.(밑의 상황(1))
  - 근데 System call, Interrupt는 CPU제어권이 사용자 프로세스 → OS이기 때문에 이 경우에는 일어나지 않음. (밑의 상황(2))

![Untitled](Process%20781ff64f806e463b9001b9ceaaf51656/Untitled%206.png)

(1)의 경우에도 CPU 수행 정보 등 context의 일부를 PCB에 save해야 하지만 문맥교환을 하는 (2)의 경우 그 부담이 훨씬 큼(eg. cache memory flush)

## 프로세스를 스케쥴링하기 위한 큐

- **Job queue** : 현재 시스템 내에 있는 모든 프로세스의 집합
- **Ready queue** : 현재 메모리 내에 있으면서 CPU를 잡아서 실행되기를 기다리는 프로세스의 집합
- **Device queues** : I/O device의 처리를 기다리는 프로세스의 집합
- 프로세스들은 각 큐들을 오가며 수행된다.

![Untitled](Process%20781ff64f806e463b9001b9ceaaf51656/Untitled%207.png)

![interrupt occurs 후에는 정확히는 ready queue로 가는 것은 아님](Process%20781ff64f806e463b9001b9ceaaf51656/Untitled%208.png)

interrupt occurs 후에는 정확히는 ready queue로 가는 것은 아님

## Scheduler

- **Long-term scheduler** (job scheduler or 장기 스케쥴러)
  - 시작 프로세스 중 어떤 것들을 ready queue로 보낼지 결정
    - Process state에서 new에 있는 것을 ready로 보낼지말지 결정
  - 프로세스에 memory(및 각종 자원)을 주는 문제
  - degree of Multiprogramming을 제어
    - 메모리에 올라간 프로그램의 수를 제어
  - time sharing system에는 보통 장기 스케쥴러가 없음 (무조건 ready)
- **Short-term scheduler** (CPU scheduler or 단기 스케쥴러)
  - 어떤 프로세스를 다음번에 running시킬지 결정
  - 프로세스에 CPU를 주는 문제
  - 충분히 빨라야 함(millisecond 단위)
- **Medium-term scheduler** (Swapper or 중기 스케쥴러)
  - 여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫓아냄
  - 프로세스에게서 memory를 뺏는 문제
  - degree of Multiprogramming을 제어

## Thread

“A thread(or lightweight process) is a basic unit of CPU utilization”

프로세스의 내부에 CPU 시행 단위가 여러개 있는 경우를 뜻함

- Thread의 구성(=Thread마다 가지고 있는 고유 정보)
  - program counter
  - register set
  - stack space
- Thread가 동료 thread와 공유하는 부분(=task)
  - code section
  - data section
  - OS resources
- 전통적인 개념의 heavyweight process는 하나의 thread를 가지고 있는 task로 볼 수 있다.

### Thread의 장점

- **Responsiveness(응답성)**
  - eg) multi-threaded Web - If one thread is blocked(eg. network), another thread continues( eg. display)
    → 예시로 어떤 사이트를 들어가면 html을 먼저 보여주고 이미지 파일들을 웹에 요청을 보내는데, 싱글스레드면 이미지 요청이 끝날때까지 blocked 돼서 아무것도 보여주지 않음. 그러나 multi thread면 blocked된 것은 두고 실행이 가능한 것들(html)은 실행이 돼 유저에게 보여짐⇒비동기식 입출력(사진이 나오던말던 나머지는 실행되니까)
- **Resource Sharing(자원 공유)**
  - n threads can share binary code, data, resource of the process → 효율적
- **Economy(경제성)**
  - creating & CPU switching thread ( rather than a process)
  - Solaris의 경우 위 두 가지 overhead가 각각 30배, 5배
- **Utilization of MP(Multi Processor) Architectures**

  - each thread may be running in parallel on a different processor

- 다중 스레드로 구성된 태스크 구조에서는 하나의 서버 스레드가 blocked(waiting)상태인 동안에도 동일한 태스크 내의 다른 스레드가 실행(running)되어 빠른 처리를 할 수 있다.
- 동일한 일을 수행하는 다중 스레드가 협력하여 높은 처리율(throughput)과 성능 향상을 얻을 수 있다.
- 스레드를 사용하면 병렬성을 높일 수 있다.→CPU가 여러개 있는 컴퓨터에서 얻는 장점

![Untitled](Process%20781ff64f806e463b9001b9ceaaf51656/Untitled%209.png)

프로세스 하나에 cpu실행단위만 여러개를 Thread라고함

→ 공유가능한 것들은 최대한 공유(메모리주소공간, process state, 자원)

→ 다른 것은 cpu 수행과 관련된 정보.

![Untitled](Process%20781ff64f806e463b9001b9ceaaf51656/Untitled%2010.png)

![Untitled](Process%20781ff64f806e463b9001b9ceaaf51656/Untitled%2011.png)

### Implementation of Thread(구현 방식)

- Kernel로 구현 → Kernel Thread
  스레드가 여러개 있다는걸 커널이 알고 있음
  - Windows 95/98/NT
  - Solaris
  - Digital UNIX, Mach
- library로 구현 → User Threads
  운영체제는 스레드가 여러개인걸 모름. 프로세스 본인이 내부에서 관리를 함→ 제약 조건이 좀 있음
  - POSIX Pthreads
  - Mach C-threads
  - Solaris threads
- Some are real-time threads
