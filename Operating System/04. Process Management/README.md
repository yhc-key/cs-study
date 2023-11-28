# Process Management

- [Process Management](#process-management)
  - [Process Creation](#process-creation)
  - [Process Termination](#process-termination)
  - [fork() 시스템 콜](#fork-시스템-콜)
  - [exec() 시스템 콜](#exec-시스템-콜)
  - [wait() 시스템 콜](#wait-시스템-콜)
  - [exit() 시스템 콜](#exit-시스템-콜)
  - [프로세스 간 협력](#프로세스-간-협력)
    - [Message Passing](#message-passing)
    - [Shared Memory](#shared-memory)

## Process Creation

- Parent Process(부모 프로세스)가 Children Process(자식 프로세스) 생성
- 프로세스의 트리(계층 구조) 형성
- 프로세스는 자원을 필요로 함
  - 운영체제로부터 받는다
  - 부모와 공유한다
- 자원의 공유
  - 부모와 자식이 모든 자원을 공유하는 모델
    - 리눅스같은 효율적인 모델은 우선 자식이 부모에게서 복제되면 새로운 프로세스를 만들지 않고 자식이 부모 프로세스의 주소값을 가짐. 그리고 변경이 생기면 부모의 메모리 공간을 카피해서 자신의 메모리 공간을 갖게 됨(Copy-on-Write(COW))
  - 일부를 공유하는 모델
  - 전혀 공유하지 않는 모델→이게 일반적
- Execution(수행)
  - 부모와 자식은 공존하며 수행되는 모델
  - 자식이 terminate(종료)될 때까지 부모가 wait(blocked 상태,기다리는) 모델
- Address space(주소 공간)
  - 자식은 부모의 공간을 복사함(binary and OS data)
  - 자식은 그 공간에 새로운 프로그램을 올림
- 유닉스의 예
  - **fork()** 시스템 콜이 새로운 프로세스를 생성
    - 부모를 그대로 복사(OS data except PID + binary)
    - 주소 공간 할당
  - fork 다음에 이어지는 **exec()** 시스템 콜을 통해 새로운 프로그램을 메모리에 올림

## Process Termination

- 프로세스가 마지막 명령을 수행한 후 운영체제에게 이를 알려줌(**exit**) → 자발적 종료
  - 자식이 부모에게 output data를 보냄 (via wait)
  - 프로세스의 각종 자원들이 운영체제에게 반납됨
- 부모 프로세스가 자식의 수행을 종료시킴(abort) → 강제 종료
  - 자식이 할당 자원의 한계치를 넘어섬
  - 자식에게 할당된 태스크가 더 이상 필요하지 않음
  - 부모가 종료(exit)하는 경우
    - 운영체제는 부모 프로세스가 종료하는 경우 자식이 더 이상 수행되도록 두지 않는다
    - 단계적인 종료

## fork() 시스템 콜

Creates a child(copy).

![Untitled](Process%20Management%204f1bdc915aac4c0d8af1bd634a91c887/Untitled.png)

이렇게 fork()를 하면 자식이 복제가 되고, 자식은 자기가 fork()된 사실을 알고 있어서 fork()밑의 줄부터 실행된다. 그래서 fork()의 무한루프에 빠지지 않음.

자식과 부모의 구분을 위해 pid값(fork()의 결과값)을 리턴을 받는다. 자식은 0, 부모는 양수.

## exec() 시스템 콜

Overlays a new image.

![Untitled](Process%20Management%204f1bdc915aac4c0d8af1bd634a91c887/Untitled%201.png)

exec()을 하면 기존의 부모에게서 복제된 기억을 잊고 함수내의 내용으로 덮어써서 처음부터 실행함

![Untitled](Process%20Management%204f1bdc915aac4c0d8af1bd634a91c887/Untitled%202.png)

이런 식으로 fork()가 exec()의 필수조건은 아님. 그냥 혼자 exec()도 사용 가능

## wait() 시스템 콜

Sleeps until child is done.

- 프로세스 A가 wait() 시스템 콜을 호출하면
  - 커널은 child가 종료될 때까지 프로세스 A를 sleep 시킨다(block 상태)
  - Child process가 종료되면 커널은 프로세스 A를 깨운다(ready 상태)

![Untitled](Process%20Management%204f1bdc915aac4c0d8af1bd634a91c887/Untitled%203.png)

## exit() 시스템 콜

Frees all the resources, notify parent.

- 프로세스의 종료
  - 자발적 종료
    - 마지막 statement 수행 후 exit() 시스템 콜을 통해
    - 프로그램에 명시적으로 적어주지 않아도 main 함수가 리턴되는 위치에 컴파일러가 넣어줌
  - 비자발적 종료
    - 부모 프로세스가 자식 프로세스를 강제 종료시킴
      - 자식 프로세스가 한계치를 넘어서는 자원 요청
      - 자식에게 할당된 태스크가 더 이상 필요하지 않음
      - 키보드로 kill, break 등을 친 경우
      - 부모가 종료하는 경우
        - 부모 프로세스가 종료하기 전에 자식들이 먼저 종료됨

## 프로세스 간 협력

- Independent process(독립적 프로세스)
  - 프로세스는 각자의 주소 공간을 가지고 수행되므로 원칙적으로 하나의 프로세스는 다른 프로세스의 수행에 영향을 미치지 못함
- Cooperating process(협력 프로세스)
  - 프로세스 협력 메커니즘을 통해 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있음
- **IPC**(Interprocess Communication, 프로세스 간 협력 메커니즘)
  - 메세지를 전달하는 방법
    - **message passing** : 커널을 통해 메세지 전달
  - 주소 공간을 공유하는 방법
    - **shared memory** : 서로 다른 프로세스 간에도 일부 주소 공간을 공유하게 하는 shared memory 메커니즘이 있음
    참고) **thread** : thread는 사실상 하나의 프로세스(CPU 단위임)이므로 프로세스 간 협력으로 보기는 어렵지만, 동일한 process를 구성하는 thread들 간에는 주소 공간을 공유하므로 협력이 가능.
  ![Untitled](Process%20Management%204f1bdc915aac4c0d8af1bd634a91c887/Untitled%204.png)

### Message Passing

- Message System
  - 프로세스 사이에 shared variable(공유변수)를 일체 사용하지 않고 통신하는 시스템
  - **Direct Communication**
    - 통신하려는 프로세스의 이름을 명시적으로 표시
    ![Untitled](Process%20Management%204f1bdc915aac4c0d8af1bd634a91c887/Untitled%205.png)
  - **Indirect Communication**
    - mailbox(또는 port)를 통해 메세지를 간접 전달
    - 아무나 문어발식으로 메세지가 갈 수도 있음
    ![Untitled](Process%20Management%204f1bdc915aac4c0d8af1bd634a91c887/Untitled%206.png)

### Shared Memory

커널에게 shared memory를 한다고 먼저 system call을 해준 후, 공유한다.
