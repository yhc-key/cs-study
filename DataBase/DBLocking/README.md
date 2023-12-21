# DBLocking

## Locking이란?
- 데이터의 일관성을 보장하기 위한 방법
- 다수의 트랜잭션이 동일한 데이터에 동시에 접근하려 할 때, Locking을 통해 한 번에 하나의 트랜잭션만이 해당 데이터에 접근할 수 있도록 함.
- 데이터의 일관성 및 동시성 문제를 해결하기 위한 수단

## Lock의 종류

### 1. Shared Lock(공유 Lock or Read Lock)
- 데이터를 읽을 때 사용
- 데이터에 Lock은 걸었지만 다른 세션에서 읽을 수는 있음.
- 공유 Lock 설정 시 추가로 공유 Lock을 설정할 수 있지만, 배타적 Lock은 설정 불가.
- 내가 보고 있는 데이터는 보는 것은 가능, 변경은 불가

### 2. Exclusive Lock(배타적 Lock 또는 Write lock)
- 데이터 변경 시 사용
- 해당 Lock 해제 전까지 다른 공유 Lock, 배타적 Lock 설정 불가(본인이 Lock을 건 후에 이 데이터에 대한 읽기, 쓰기 불가)

## Blocking이란?
- 잘못된 Locking 전략, or 불안정한 상황에 발생할 수 있음
- 특정 트랜잭션이 Lock 획득 시 다른 트랜잭션이 Lock을 얻기위해 대기하는 상황

### Blocking 방지 방법
1. 적절한 Lock 범위 설정
2. Lock 유지 시간 최소화
3. Deadlock 방지