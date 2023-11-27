# System Structure & Program Execution

- [System Structure \& Program Execution](#system-structure--program-execution)
- [System Structure](#system-structure)
  - [동작 과정](#동작-과정)
  - [Mode bit](#mode-bit)
  - [Timer](#timer)
  - [Device Controller](#device-controller)
- [I/O Device Controller](#io-device-controller)
  - [Device Driver(장치구동기)](#device-driver장치구동기)
  - [Device Controller(장치제어기)](#device-controller장치제어기)
  - [I/O의 수행](#io의-수행)
  - [Interrupt](#interrupt)
    - [관련 용어](#관련-용어)
    - [I/O 수행 시 일어나는 Interrupt](#io-수행-시-일어나는-interrupt)
  - [System Call](#system-call)
  - [Synchronous I/O \& Asynchronous I/O](#synchronous-io--asynchronous-io)
  - [DMA](#dma)
  - [I/O 할 수 있는 방법](#io-할-수-있는-방법)
    - [I/O를 수행하는 Special Instruction에 의해](#io를-수행하는-special-instruction에-의해)
    - [Memory Mapped I/O에 의해](#memory-mapped-io에-의해)
  - [저장장치 계층 구조](#저장장치-계층-구조)
- [프로그램의 실행(Memory Load)](#프로그램의-실행memory-load)
  - [커널 주소 공간의 내용](#커널-주소-공간의-내용)
    - [Code 부분](#code-부분)
    - [Data 부분](#data-부분)
    - [Stack 부분](#stack-부분)
  - [사용자 프로그램이 사용하는 함수](#사용자-프로그램이-사용하는-함수)
  - [프로그램 실행 단계](#프로그램-실행-단계)

# System Structure

![Untitled](System%20Structure%20&%20Program%20Execution%20edd1257afbcb40758a2e3f23bb3bd1f8/Untitled.png)

- CPU : 메모리에서 인스트럭션을 읽어서 시행하는 역할
  - register : CPU보다 빠르게 정보를 저장하는 공간
  - mode bit : CPU에서 실행되는게 운영체제인지, 사용자 프로그램인지 구분
  - interrupt line : I/O device가 신호를 보낼때 그걸 CPU에게 전달
- 메모리 : 작업공간
- I/O device : 개별적
  - Disk : I/O 둘다 함
  - device controller : 디바이스를 전담하는 작은 CPU 역할
  - local buffer : device controller의 작업공간
- Timer : 무한 루프를 도는 프로그램이 있으면 CPU를 독점해버려서 다른 것을 할 수 없음. 그래서 타이머가 특정 프로그램이 CPU를 독점하는 것을 막음.
- DMA Controller : 중간중간 I/O가 너무 interrupt를 CPU에 걸어 비효율적→ I/O의 interrupt를 DMA가 메모리로 복사해 얘가 CPU에게 한번에 인터럽트를 걺.

## 동작 과정

- **상황 1 : 사용자 프로그램이 키보드 눌러야 하는 거임**

CPU는 메모리한테서 인스트럭션을 읽음. → 디스크에서 뭘 읽어야한다 → 디바이스 컨트롤러한테 특정 디바이스 것을 읽어오라고 시킴 → 그리고 IO가 실행됨(e.g. 키보드가 눌림) → interrupt에 신호를 보냄 → 이 상황이 느리게 일어나는데, CPU는 이때 놀지 않고 메모리에 계속 접근 → interrupt에 걸리면 다시 그 사용자 프로그램에게 CPU 제어권이 주어짐

- **상황 2 : 무한 루프를 도는 프로그램임**

CPU에서 무한루프를 도는 사용자 프로그램임 → 타이머 시간이 끝나면 인터럽트에 신호를 줌 → 자동으로 CPU 제어권이 사용자 프로그램에서 OS 로 넘어감

- **상황 3 : 클래스에서 메서드를 호출할 때**

그냥 사용자프로그램 내에서 메모리 주소만 바꾸면서 순차적으로 실행이 됨

- **상황 4 : 메서드에서 I/O 호출을 해야할 때(system call)**

사용자 프로그램에서 우선 OS로 넘어가야함→하기(mode bit이 현재 1이라 바로 OS로 못넘어감)

사용자 프로그램이 직접 Interrupt line 실행→ Mode bit 0으로 바뀜 → CPU제어권 OS로 넘어감→ I/O를 위한 system call

## Mode bit

사용자 프로그램의 잘못된 수행으로 다른 프로그램 및 운영체제에 피해가 가지 않도록 하기 위한 보호 장치 필요

- Mode bit을 통해 하드웨어적으로 두 가지 모드의 operation 지원
  - 1 사용자 모드 : 사용자 프로그램 수행
  - 0 모니터 모드(=커널 모드, 시스템 모드) : OS 코드 수행
  - 보안을 해칠 수 있는 중요한 명령어는 모니터 모드에서만 수행 가능한 ‘**특권명령**’으로 규정
  - Interrupt나 Exception 발생 시 하드웨어가 mode bit을 0으로 바꿈
  - 사용자 프로그램에게 CPU를 넘기기 전에 mode bit을 1로 셋팅

## Timer

- 정해진 시간이 흐른 뒤 운영체제에게 제어권이 넘어가도록 인터럽트를 발생시킴
- 타이머는 매 클럭 틱 때마다 1씩 감소
- 타이머 값이 0이 되면 타이머 인터럽트 발생
- CPU를 특정 프로그램이 독점하는 것으로부터 보호
- Time sharing을 구현하기 위해 널리 이용됨
- 현재 시간을 계산하기 위해서도 사용

## Device Controller

# I/O Device Controller

- 해당 I/O 장치를 관리하는 일종의 작은 CPU
- 제어 정보를 위해 control register, status register을 가짐
- local buffer을 가짐 ( 일종의 data register)
- I/O는 실제 device와 local buffer 사이에서 일어남
- Device Controller는 I/O가 끝났을 경우 interrupt로 CPU에 그 사실을 알림

### Device Driver(장치구동기)

OS 코드 중 각 장치별 처리루틴 → Software

### Device Controller(장치제어기)

각 장치를 통제하는 일종의 작은 CPU → Hardware

## I/O의 수행

모든 입출력 명령은 특권 명령

사용자 프로그램은 어떻게 I/O를 할까?

- System call(시스템콜)
  - 사용자 프로그램은 운영체제에게 I/O를 요청
- Trap을 사용해 인터럽트 벡터의 특정 위치로 이동
- 제어권이 인터럽트 벡터가 가리키는 인터럽트 서비스 루틴으로 이동
- 올바른 I/O요청인지 확인 후 I/O 수행
- I/O 완료 시 제어권을 시스템콜 다음 명령으로 옮김

## Interrupt

인터럽트 당한 시점의 레지스터와 program counter을 save한 후 CPU의 제어를 인터럽트 처리 루틴에 넘긴다

- 넓은 의미의 Interrupt
  - Interrupt(Hardware Interrupt): 하드웨어가 발생시킨 인터럽트
  - Trap(Software Interrupt)
    - Exception : 프로그램이 오류를 범한 경우
    - System call : 프로그램이 커널 함수를 호출하는 경우

### 관련 용어

- Interrupt Vector(인터럽트 벡터)
  : 해당 인터럽트의 처리 루틴 주소를 가지고 있음. 인터럽트마다 처리해야할 함수의 주소를 적어놓은 것
- Interrupt Service Routine(인터럽트 처리 루틴 / 인터럽트 핸들러)
  : 해당 인터럽트를 처리하는 커널 함수

### I/O 수행 시 일어나는 Interrupt

사용자 프로그램이 os에게 system call(이때는 software interrupt발생) → 시킨일 다끝나면 I/O가 CPU 에게 hardware interrupt

## System Call

사용자 프로그램이 운영체제의 서비스를 받기 위해 커널 함수를 호출하는 것

## Synchronous I/O & Asynchronous I/O

- Synchronous I/O(동기식 입출력)
  - I/O 요청 후 입출력 작업이 완료된 후에야 제어가 사용자 프로그램에 넘어감
  - 구현 방법 1
    - I/O가 끝날 때까지 CPU를 낭비시킴
    - 매시점 하나의 I/O만 일어날 수 있음
  - 구현 방법 2(주로 이런식으로 구현)
    - I/O가 완료될 때까지 해당 프로그램에게서 CPU를 빼앗음
    - I/O 처리를 기다리는 줄에 그 프로그램을 줄 세움
    - 다른 프로그램에게 CPU를 줌
- Asynchronous I/O(비동기식 입출력)
  - I/O가 시작된 후 입출력 작업이 끝나기를 기다리지 않고 제어가 사용자 프로그램에 즉시 넘어감

⇒ 두 경우 모두 I/O의 완료는 Interrupt로 알려줌

![Untitled](System%20Structure%20&%20Program%20Execution%20edd1257afbcb40758a2e3f23bb3bd1f8/Untitled%201.png)

## DMA

Direct Memory Access

- 빠른 입출력 장치를 메모리에 가까운 속도로 처리하기 위해 사용
- CPU의 중재없이 device controller가 device의 buffer storage의 내용을 메모리에 block 단위로 직접 전송
- 바이트 단위가 아닌 block 단위로 인터럽트 발생시킴

## I/O 할 수 있는 방법

### I/O를 수행하는 Special Instruction에 의해

이게 일반적인 방식

![Untitled](System%20Structure%20&%20Program%20Execution%20edd1257afbcb40758a2e3f23bb3bd1f8/Untitled%202.png)

메모리 접근하는 instruction 따로, I/O디바이스 접근하는 특별한 instruction 따로

### Memory Mapped I/O에 의해

![Untitled](System%20Structure%20&%20Program%20Execution%20edd1257afbcb40758a2e3f23bb3bd1f8/Untitled%203.png)

I/O 디바이스까지 메모리 주소를 줘서 부름

## 저장장치 계층 구조

![Untitled](System%20Structure%20&%20Program%20Execution%20edd1257afbcb40758a2e3f23bb3bd1f8/Untitled%204.png)

위로갈수록 빠르고, 비싸고, 용량이 작음. 연두색이 휘발성 매체, 아래가 비휘발성 매체임.

연두 - CPU가 직접 접근 가능. Byte 단위 접근 가넝

캐싱 재사용 요청

# 프로그램의 실행(Memory Load)

![Untitled](System%20Structure%20&%20Program%20Execution%20edd1257afbcb40758a2e3f23bb3bd1f8/Untitled%205.png)

- 커널 영역은 컴터를 키면 무조건 실행됨
- 다른건 프로그램이 꺼지면 사라짐
- 주소 공간중 당장 필요한걸 Physical memory에 두고 아닌건 Swap area에 둠
- File System-전원이 나가도 유지되는 곳
- Swap area - 전원이 나가면 끝인 곳
- Address translation - 논리적인 메모리 주소가 물리적인 메모리 주소로 변환됨

## 커널 주소 공간의 내용

![Untitled](System%20Structure%20&%20Program%20Execution%20edd1257afbcb40758a2e3f23bb3bd1f8/Untitled%206.png)

### Code 부분

- 자원을 효율적으로 관리하는 코드, 인터럽트가 들어오면 무슨일을 처리해야하는지 적힌 코드가 있음

### Data 부분

- 운영체제가 사용하는 여러 자료구조들이 있음
- 실행중인 프로그램 관리하는 자료구조(PCB)

### Stack 부분

- 사용자 프로그램마다 커널 스택이 따로 있음

## 사용자 프로그램이 사용하는 함수

**프로세스의 Address space**

- **사용자 정의 함수**
  - 자신의 프로그램에서 정의한 함수
  - 내가 만든거
- **라이브러리 함수**
  - 자신의 프로그램에서 정의하지 않고 갖다 쓴 함수
  - 자신의 프로그램의 실행 파일에 포함되어 있다

**Kernel Address space**

- **커널 함수**
  - 운영체제 프로그램의 함수
  - 커널 함수의 호출 = 시스템 콜

## 프로그램 실행 단계

![Untitled](System%20Structure%20&%20Program%20Execution%20edd1257afbcb40758a2e3f23bb3bd1f8/Untitled%207.png)
