- [GC 도입의 전재 가설(Week Generational Hypothesis)](#gc-도입의-전재-가설week-generational-hypothesis)
- [GC의 역할](#gc의-역할)
- [GC 수행 영역별 구분](#gc-수행-영역별-구분)
  - [Heap 영역](#heap-영역)
    - [Minor GC](#minor-gc)
    - [Major GC(Full GC)](#major-gcfull-gc)
    - [GC 발생 시나리오](#gc-발생-시나리오)
  - [GC의 동작방식](#gc의-동작방식)
    - [Stop The World](#stop-the-world)
    - [Mark \& Sweep](#mark--sweep)
    - [Compact](#compact)
  - [GC의 알고리즘](#gc의-알고리즘)
    - [Serial GC](#serial-gc)
    - [Parallel GC(Throughput GC)](#parallel-gcthroughput-gc)
    - [Parallel Old GC](#parallel-old-gc)
    - [CMS Collector(Concurrent Mark-Sweep)](#cms-collectorconcurrent-mark-sweep)
    - [G1 GC(Garbage First)](#g1-gcgarbage-first)

# GC 도입의 전재 가설(Week Generational Hypothesis)

- 대부분의 객체는 금방 접근 불가능한 상태(Unreachable)가 된다.
- 오래된 객체에서 젊은 객체로의 참조 또한 아주 적게 발생함

→ 오래된 객체의 메모리 점유를 막기 위해 GC가 도입됨

# GC의 역할

1. 메모리 할당
2. 사용 중인 메모리 인식
3. 사용하지 않는 메모리 인식

# GC 수행 영역별 구분

- **JVM 메모리 영역**

JVM의 메모리는 크게 클래스 영역, 자바 스택, 힙, 네이티브 메소드 스택으로 이뤄져 있다.

GC는 Heap 메모리에서 이루어진다.

![Untitled](Garbage%20Collector%20eed40968dfe44e8d8ddbfc812fad0dec/Untitled.png)

## Heap 영역

- **New/Young 영역** : 생성된 지 얼마 안된 객체를 저장하고 시간이 지나 우선순위가 낮아지면 Old영역으로 옮겨짐. 이 영역의 GC를 Minor GC라고 부름.
- **Old 영역** : 생성된 지 오래된 객체 저장. 이 영역의 GC를 Major GC라고 부름.
- **Perm 영역** : Class, Method 등의 코드가 저장되는 영역. 자바 언어 레벨에서 사용되지 않음

### Minor GC

New/Young 영역의 GC를 `Minor GC`라고 부른다. 여기에 사용되는 알고리즘을 `Copy & Scavenge`라고 부른다. Eden / Survivor이라는 영역으로 나뉨.

- **Eden**
  - 자바 객체가 생성되자마자 저장되는 곳
  - 객체가 계속 생성돼 Eden 영역이 다 차면 Minor GC가 발생하고, 살아남은 객체는 Survivor 영역 중 하나로 이동.
- **Survivor**
  - 2개로 나뉘지만 우선순위가 있진 않음
  - 한 영역이 차면 그 중 살아남은 객체를 다른 영역으로 이동
  - 가득찬 Survivor영역은 아무 데이터가 없는 상태가 됨
    - 두 영역 중 하나는 반드시 비어있는 상태여야 함. 아니면 시스템이 비정상적인 것임.
  - 더 큰 객체가 생성되거나, 더 이상 Young 영역에 공간이 없으면 Survivor영역에서 오래된 객체를 Old 영역으로 이동(이걸 Promotion이라고 부름)
    > Object Aging
    > Survivor 영역에서 Survivor 영역으로 이동할 때마다 객체의 살아남은 횟수를 나타내는 age값을 증가시킴. Object Header에 기록한다.

### Major GC(Full GC)

Old 영역의 GC를 `Major GC`라고 부르며, 이에 사용되는 알고리즘을 `Mark & Compact`라고 부름. Minor GC과정에서 삭제되지 않고, Old Generation 영역으로 옮겨진 객체 중 미사용된다고 판단되는 객체를 메모리에서 삭제함.

속도가 매우 느려 Major GC가 일어나는 도중에는 순간적으로 자바 애플리케이션이 멈춰버려서 Major GC가 일어나는 정도와 시간이 애플리케이션의 성능과 안정성에 아주 큰 영향을 미친다.

### GC 발생 시나리오

![https://github.com/devSquad-study/2023-CS-Study/raw/main/java/img/java_gc_02.png](https://github.com/devSquad-study/2023-CS-Study/raw/main/java/img/java_gc_02.png)

1. 객체가 생성되면 `Eden`영역에 위치하게 된다.

![https://github.com/devSquad-study/2023-CS-Study/raw/main/java/img/java_gc_03.png](https://github.com/devSquad-study/2023-CS-Study/raw/main/java/img/java_gc_03.png)

2. `Eden`영역이 가득차게 되면 `Minor GC`가 발생하여 참조가 없는 객체는 삭제되고, 참조 중인 객체는 `Survivor` 영역으로 이동한다.

![https://github.com/devSquad-study/2023-CS-Study/raw/main/java/img/java_gc_04.png](https://github.com/devSquad-study/2023-CS-Study/raw/main/java/img/java_gc_04.png)

3. `Survivor`영역이 가득차게 되면, `Minor GC`가 발생하여 참조가 없는 객체는 삭제되고, 참조 중인 객체는 다른 `Survivor`영역으로 이동한다.

![https://github.com/devSquad-study/2023-CS-Study/raw/main/java/img/java_gc_05.png](https://github.com/devSquad-study/2023-CS-Study/raw/main/java/img/java_gc_05.png)

4. `Survivor`영역에서 GC과정을 반복하며, 계속 참조 중인 객체는 `Old`영역으로 이동한다.

![https://github.com/devSquad-study/2023-CS-Study/raw/main/java/img/java_gc_06.png](https://github.com/devSquad-study/2023-CS-Study/raw/main/java/img/java_gc_06.png)

5. `Eden`영역에서 `Survivor`영역으로 이동할 때 객체가 남아있는 영역보다 클 경우 `Old`영역으로 이동한다.

## GC의 동작방식

### Stop The World

GC 실행을 위해 JVM이 애플리케이션의 실행을 멈추는 작업

GC를 실행하는 thread를 제외한 모든 thread들의 작업을 중단하고, GC가 완료되면 작업이 재개됨.

### Mark & Sweep

Stop The World로 모든 작업이 중단되면 GC는 스택의 모든 변수와 Reachable 객체를 스캔하며 어떤 객체를 참고하고 있는지를 탐색한다. 그리고 참조되는 변수를 구별해서 참조되지 않는 변수를 해제한다.

- Mark : 사용되는 메모리와 사용되지 않는 메모리를 식별하는 작업
- Sweep : Mark 단계에서 사용되지 않음으로 식별된 메모리를 해제하는 작업

### Compact

Compaction : 객체들을 가까운 곳으로 모으고, heap 메모리의 아래쪽으로 보냄

![Untitled](Garbage%20Collector%20eed40968dfe44e8d8ddbfc812fad0dec/Untitled%201.png)

메모리가 GC를 거쳐 파편화(메모리 내 비어있는 공간이 서로 떨어져 있어 크기가 큰 객체를 할당하기 어려워지는 것)가 될 수 있기 때문에 이를 최소화하기 위해 JVM은 Major GC 때마다 Generation에 Compaction을 수행함.

## GC의 알고리즘

### Serial GC

![Untitled](Garbage%20Collector%20eed40968dfe44e8d8ddbfc812fad0dec/Untitled%202.png)

- 32bit JVM 단일 스레드로 수행돼 CPU Core가 한 개인 환경에서도 사용 가능.(기본 GC이다)
- Mark-Compact Collection 알고리즘을 사용함.
- Compact 기능이 추가됨.

### Parallel GC(Throughput GC)

![Untitled](Garbage%20Collector%20eed40968dfe44e8d8ddbfc812fad0dec/Untitled%203.png)

- 기본적으로 Serial GC와 동작과정은 동일하지만, Minor GC에서 사용되는 thread수를 늘릴 수 있다.
- Minor GC는 멀티 스레딩, Major GC는 싱글 스레딩으로 수행함.
- 다중 스레드로 수행됨.
- Serial GC보다 빠름.
- GC의 오버헤드는 많이 줄여주지만 애플리케이션이 멈추는 것은 여전함.

### Parallel Old GC

- **Java 1.8의 기본 GC임.**
- Parallel GC와 다른점은 **Major GC도 멀티 스레드**임.

### CMS Collector(Concurrent Mark-Sweep)

![Untitled](Garbage%20Collector%20eed40968dfe44e8d8ddbfc812fad0dec/Untitled%204.png)

- Stop The World로 인한 중단 시간이 짧아 응답시간이 중요할 때 사용하기 좋음.
- **동작방식**
  - **Initial Mark** 단계 : 클래스 로더에서 가장 가까운 객체 중 살아있는 객체만 찾는 것으로 끝냄(멈추는 시간이 매우 짧다)
  - **Concurrent Mark** 단계 : 참조상태 객체를 확인, `stop-the-world 없이` 다른 스레드가 실행 중인 상태에서 동시에 실행된다.
  - **Remark** 단계 : Concurrent 단계에서 새로 추가되거나 참조가 끊긴 객체를 확인
  - **Concurrent Sweep** 단계 : 참조 되지 않은 객체를 정리(쓰레기 정리), 다른 스레드가 실행되고 있는 상태에서 진행 됨
- `Major GC`에 소요되는 작업을 애플리케이션을 멈추고 진행하는 것이 아니라, 일부는 애플리케이션이 돌아가는 단계에서 수행하고 최소한의 작업만을 애플리케이션이 멈췄을 때 수행하는 방식
- 메모리와 CPU 리소스가 더 많이 소비된다.
- Compaction 기능이 제공되지 않음.

### G1 GC(Garbage First)

![Untitled](Garbage%20Collector%20eed40968dfe44e8d8ddbfc812fad0dec/Untitled%205.png)

- 바둑판처럼 Heap 메모리 영역을 나누어 관리함.
- 기존 Young, Old 영역의 개념과 다른 Heap에 `Region` 개념을 도입함
  - Humonogous : Region 크기의 50%를 초과하는 큰 객체를 저장하기 위한 공간
  - Available/Unused : 아직 사용되지 않은 Region
- 하나 이상의 Region에서 객체를 복사하여 다른 Region으로 이동시키는 방식
- CMS와 비슷한 방식으로 동작 시작
  - Heap에 전역적으로 Marking
  - 가장 많이 공간이 있는 곳부터 메모리 회수 진행 *=> 이 때문에 Garbage First라는 이름이 붙었다.*
- Stop The World로 인한 중단 시간이 짧음.
- Compaction 기능을 제공함.
- **Java 9부터 기본 GC**로 채택됨.
