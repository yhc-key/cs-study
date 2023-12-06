# 18. HashMap

자바에서 HashMap에 대해 알아보기 전, Hash에 대해서 알아보자.

<br>

# ✨Hash✨

대략적인 개념들부터 살펴보자.

### ✔️ 해시함수(hash function)

데이터의 효율적 관리를 목적으로 <u>임의의 길이의 데이터</u>를 **고정된 길이의 데이터**로 매핑하는 함수

해시함수의 해시값이 최대한 **균등하게** 나오게 하는게 중요하다!

<br>

### ✔️ 해싱(hashing)

- `키`(key) : 매핑 전 원래 데이터의 값
- `해시값`(hash value) : 매핑 후 데이터의 값

매핑하는 과정 자체를 해싱이라 한다.

<br>

### ✔️ 해시충돌(collision)

해시함수는 해시값의 개수보다 대개 많은 키값을 해쉬값으로 변환(many-to-one 대응) 하기 때문에 **해시함수가 서로 다른 두 개의 키에 대해 동일한 해시값**을 내는 해시충돌이 발생하게 된다.

그래서 <u>충돌을 최소화할 수 있는 해시함수</u>를 사용해야한다.

<div align='center'>
    <img src="img/hashmap_1.png" width="500px">
</div>

<br>

### ✔️ Hash 사용처

hash는 hashmap 형태로 많은 언어들에서 사용되고 있는데, 주로 특정 데이터를 임시 공간에 저장하고 나중에 데이터의 키로 해당 데이터를 빠르게 찾으려고 할 때 많이 사용된다.

대표적으로 **캐싱**을 할 때 많이 사용되며, key-value store라고 불리는 NoSQL(Redis, etcd 등) 같은 친들이 이러한 해싱 방식을 사용한다.

<br>

# ✨Hash table (hash map)✨

## 📌 Map

- `정의` : 특정 순서에 따라 키와 매핑된 값의 조합으로 형성된 자료구조
- `구현체` : hash table, tree-based

<br>

## 📌 Hash table (hash map)

- `정의` : 배열과 해시 함수(hash function)를 사용하여 map을 구현한 자료구조
- 키에 대한 해시 값을 사용하여 값을 저장하고 조회하며, 키-값 쌍의 개수에 따라 동적으로 크기가 증가하는 associate array
- 그래서 일반적으로 <u>상수 시간으로 데이터에 접근</u>하기 때문에 빠르다.

- hash table에서 해시함수는 임의의 데이터를 **정수**로 변환해 줌.

이때 데이터가 저장되는 곳은 **버킷(bucket)** 또는 **슬롯(slot)** 이라고 한다.

- 메모리를 절약하기 위하여 객체에 대한 해시 코드의 <u>나머지 값을 해시 버킷 인덱스 값으로 사용</u>한다.

(HashMap을 비롯한 많은 해시 함수를 이용하는 associative array 구현체에서)

- **저장/조회할 해시 버킷을 계산하는 방법**

  ```java
  int index = X.hashCode() % M;
  ```

<br>

### ✔️ 1. 데이터 삽입

잠시 예시로 데이터를 저장하는 과정을 살펴보자.

**ex. put("John Smith", "521-1234")**

<div align='center'>
    <img src="img/hashmap_2.png" width="500px">
</div>

현재 버킷의 **capacity**는 16이다.

이 경우에 key 값이 hash function에 나온 결과 **210 % 16**를 하면 값은 2가 된다.

그러면 이 2는 Index 2번 위치에 저장하라는 말이 된다.

| Index(Hash Value) |          Data          |
| :---------------: | :--------------------: |
|        01         | (Lisa Smith, 521-8976) |
|        02         | (John Smith, 521-1234) |

<br>

### ✔️ 2. 데이터 조회

**ex. get("Jeongyoon Park")**

동일하게 hash function에 값을 돌리고 **226 % 16**를 하면 값은 2가 된다.

이때 2 인덱스에 찾으러 갔을 때 Data는 (John Smith, 521-1234)가 저장되어 있다.

John Smith와 Jeongyoon Park을 비교했을 때 값이 다르기 때문에 값을 찾지 못하고 종료한다.

<br>

## 📌 Hash collision (해시 충돌)

- key는 다른데 hash가 같을 때
- key도 hash도 다른데 hash % map_capacity 결과가 같을 때

보통의 경우는 실제로 사용하는 key 개수보다 적은 해시테이블의 크기를 가질 것이다.

다뤄야 할 데이터가 정말 많고, 메모리 등 리소스 문제도 생기기 때문이다.

위에서도 간단하게 설명했지만 해시함수는 해시값의 개수보다 대개 많은 키값을 해쉬값으로 변환(many-to-one 대응) 하기 때문에 줄어든 범위에서 충돌이 발생할 수밖에 없다.

그렇기 때문에 해시함수의 해시값이 최대한 균등하게 나오게 하는 게 중요함

<br>

## 📌 해결 방법

### ✔️ 1. open addressing (개방 주소법)

해당 버킷에 데이터가 이미 있는데 key 값이 다르면 충돌이 발생한다. 이런 경우에 아래 Separate chaining과 다르게 `open addressing` 방식은 <u>비어있는 버킷</u>을 활용한다.

즉, open addressing은 <u>한 버킷당 들어갈 수 있는 엔트리가 하나뿐인</u> 해시테이블

<br>

이것을 구현하는 방법에는 **3가지**가 있다.

- **`Linear Probing`**(선형 조사법): 현재 버킷의 index에서 고정폭만큼씩 이동하여 비어 있는 버킷에 저장한다.
- **`Quadratic Probing`**(이차 조사법): 현재 버킷의 index에서 **제곱수**만큼씩 이동하여 비어 있는 버킷에 저장한다.
  - ex. 처음에는 1만큼 이동하고 그다음 계속 충돌이 발생하면 2^2, 3^2칸씩 이동하는 방식
  - 선형 조사법과 이차 조사법의 경우 충돌 횟수가 많았지만 특정 영역에 데이터가 집중적으로 몰리는 클러스터링현상이 발생하는 단점이 있고 이 경우 평균 탐색 시간이 증가한다.

<div align='center'>
    <img src="img/hashmap_3.png" width="500px">
</div>

<br>

- **`Double Hashing Probing`**(이중 해시, 중복 해시): 이중 해싱은 클러스터링 문제가 발생하지 않도록 2개의 해시 함수를 사용하는 방법이다.

<div align='center'>
    <img src="img/hashmap_4.png" width="500px"><br>
    <p>출처 : 쉽게 배우는 알고리즘 - 관계 중심의 사고법 /문병로</p>
</div>

<br>

### ✔️ 2. Separate chaining (분리 연결법)

간단한 아이디어로 한 버킷당 들어갈 수 있는 엔트리의 수에 제한을 두지 않음으로써 모든 자료를 해시테이블에 담는다.

해당 버킷에 데이터가 이미 있는데 key 값이 다르면 충돌이 발생한다. 이때 **연결리스트에 노드를 추가하여 데이터를 저장**한다.

<div align='center'>
    <img src="img/hashmap_5.png" width="500px">
</div>

### 장점

- 유연하다.

### 단점

- 메모리 문제를 야기할 수 있다.
- 데이터의 수가 많아지면 동일한 버킷에 chaining 되는 데이터가 많아져 캐시의 효율성이 감소한다.

<br>

## 📌 Java에서 Hash collision (해시 충돌)

Java HashMap에서는 **Separate channing 방식**으로 충돌을 해결한다.

왜냐하면 Open addressing 방식은 데이터 삭제 처리가 효율적이지 않기 때문이다.

이때 해시 버킷을 구현이 자바 버전에 따라 달라진다.

### ✔️ Java 7 이전

Java 7에서는 버킷 구조를 링크드 리스트만 사용

### ✔️ Java 8

Java 8에서는 데이터의 개수가 많아지면, 링크드 리스트 대신 트리를 사용

- 링크드 리스트를 사용할 것인가 트리를 사용할 것인가는 **하나의 해시 버킷에 할당된 키-값 쌍의 개수**에 따라 결정됨

- 하나의 해시 버킷에 **8개**의 키-값 쌍이 모이면 링크드 리스트를 **트리**로 변경

- 이때 사용하는 트리는 `Red-Black Tree`

  👉 [Red-Black 트리란?](https://hello-judy-world.tistory.com/199)

<br>

## 📌 Java에서 HashMap과 HashTable

HashMap과 HashTable은 Java의 API 이름이다.
이 두가지 모두 Map 인터페이스를 구현하고 있다.

**`HashTable`**

- 동기화를 지원함
  - 멀티스레드 환경에서 사용하기 좋은 자료구조
- HashMap에 비해 느리다
  - 다른 스레드가 block되고 unblock되는 대기 시간을 기다리기 때문
- 보조 해시 함수를 사용하지 않는다.

**`HashMap`**

- 동기화를 지원하지 않음
  - 단일 스레드 환경에서 사용하기 좋은 자료구조
- key값이나 value값에 null 들어갈 수 있음
- 보조 해시 함수(Additional Hash Function) 사용
  - 해시 충돌(hash collision)이 덜 발생할 수 있어 상대으로 성능상 이점

<br>

### [ 보조 해시 함수 ]

> 보조 해시 함수(supplement hash function)의 **목적**: '키'의 해시 값을 변형하여, 해시 충돌 가능성을 줄이는 것

- Java 8 HashMap 보조 해시 함수
  - 상위 16비트 값을 XOR 연산하는 매우 단순한 형태의 보조 해시 함수를 사용

---

# ✨✨

## 📌

### ✔️
