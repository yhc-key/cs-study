# 13. Stream API

# ✨Stream API✨

## 📌 Stream 정의

### ✔️ 기존 JAVA의 단점

- 많은 양의 데이터를 저장하기 위해서 배열/컬렉션 사용
  - 이렇게 저장된 데이터에 접근하기 위해서는 반복문이나 반복자(iterator)를 사용하여 매번 새로운 코드를 작성해야 함
- 이렇게 작성된 코드는 길이가 너무 길고 가독성 하락, 코드 재사용성 불가능
- 데이터 소스마다 다른 방식으로 다뤄야 한다는 단점도 존재
  - ex) List를 정렬할 때는 Collection.sort() 사용해야 하고, 배열을 정렬할 때는 Arrays.sort()를 사용해야 함

### ✔️ 스트림(Stream) API란?

- 위의 문제점들을 해결하기 위해 만든 것이 스트림
- 스트림(Stream)은 데이터를 추상화하고 데이터를 다루는데 자주 사용되는 메서들을 정의해 놓았음
  - 데이터 추상화? 데이터 소스가 무엇이던 같은 방식으로 다룰 수 있게 다룰 수 있게 되었다는 것과 코드 재사용성이 높아진다는 것을 의미
- 스트림 API를 이용하면 배열이나 컬렉션뿐만 아니라 파일에 저장된 데이터도 모두 같은 방법으로 다룰 수 있게 됨

## 📌 Stream 예시

```java
String[] fruits = {apple, grape, orange};

//Stream 쓰지 않을 때
Arrays.sort(fruits); //원본 그대로의 데이터 정렬
//출력
for(String x: fruits) {
	System.out.println(x);
    }

//Stream을 썼을 때
Stream<String> fruits_stream = Arrays.stream(fruits); //Stream 객체 생성
//정렬 후 출력
fruits_stream.sorted().forEach(System.out::println);
```

## 📌 Stream API 특징

### ✔️ Stream은 원본의 데이터를 변경하지 않음

- 스트림은 데이터 소스로부터 데이터를 읽기만 할 뿐, 데이터 소스를 변경하지 않음
- 원본 데이터를 조회하여 별도의 Stream 객체로 생성하기 때문

### ✔️ 스트림은 일회용

- 한 번 사용하면 닫혀서 재사용이 불가능.
- Stream이 또 필요할 경우 다시 생성해주어야 함.
- 닫힌 스트림을 다시 사용하면 java.lang.IllegalStateException 오류가 발생

### ✔️ 내부 반복으로 작업을 처리함

- Stream 내에서 내부적으로 반복문을 처리하기에 간결한 소스코드 작성 가능

### ✔️ 지연된 연산

- 스트림의 연산은 필터-맵(filter-map) 기반의 API를 사용하여 지연(lazy) 연산을 통해 성능을 최적화함
- 최종 연산이 수행되기 전까지는 중간 연산이 수행되지 않음
  - 스트림에 대해 distinct()나 sort() 같은 중간 연산을 호출해도 즉각적인 연산이 수행되는 것이 아니라는 뜻

### ✔️ 병렬 스트림

- 스트림은 parallel() 메소드를 통한 손쉬운 병렬 처리를 지원함
- 반대로 병렬로 처리되지 않게 하려면 sequential()을 호출하면 됨
  - 모든 스트림은 기본적으로 병렬 스트림이 아니니까 sequentail()을 호출할 필요 X
  - parallel()을 호출한 것을 취소할 때만 사용

## 📌 Stream API의 3가지 단계

1. 생성하기

   - 배열, 컬렉션, 임의의 수, 파일 등 거의 모든 것을 가지고 스트림을 생성할 수 있음.

2. 가공하기

   - 원본의 데이터를 별도의 데이터로 가공하기 위한 중간 연산의 단계.
   - 어떤 객체의 stream을 원하는 형태로 처리할 수 있으며, 중간 연산의 반환값은 stream이기 때문에 필요한 만큼 중간 연사을 연결 할 수 있음.

3. 결과 만들기
   - stream의 요소들을 소모하면서 연산이 수행되기 때문에 한번만 처리 가능.

## 📌 스트림 API의 동작 흐름

1. 스트림의 생성

2. 스트림의 중개 연산 (스트림의 변환)

3. 스트림의 최종 연산 (스트림의 사용)

# ✨스트림의 생성✨

- 스트림 API는 다양항 데이터 소스에서 생성할 수 있음.

## 📌 1. 컬렉션

- 자바에서 제공하는 모든 컬렉션의 최고 상위 조상인 Collection 인터페이스에는 stream() 메소드가 정의되어 있음
- Collection 인터페이스를 구현한 모든 List와 Set 컬렉션 클래스에서도 stream() 메소드로 스트림을 생성할 수 있음

### ✔️ 예시

```java
ArrayList<Integer> list = new ArrayList<Integer>();

list.add(4);
list.add(2);
list.add(3);
list.add(1);

// 컬렉션에서 스트림 생성
Stream<Integer> stream = list.stream();

// forEach() 메소드를 이용한 스트림 요소의 순차 접근
stream.forEach(System.out::println);
```

### ✔️ 결과

```
4
2
3
1
```

## 📌 2. 배열

- 배열에 관한 스트림을 생성하기 위해 Arrays 클래스에는 다양한 형태의 stream() 메소드가 정의되어 있음.
- 기본 타입을 저장할 수 있는 배열에 관한 스트림이 별도로 정의되어 있음.
- 이러한 스트림은 java.util.stream 패키지의 IntStream, LongStream, DoubleStream 인터페이스로 각각 제공 됨.

### ✔️ 예시

```java
String[] arr = new String[]{"넷", "둘", "셋", "하나"};

// 배열에서 스트림 생성
Stream<String> stream1 = Arrays.stream(arr);
stream1.forEach(e -> System.out.print(e + " "));
System.out.println();

// 배열의 특정 부분만을 이용한 스트림 생성
Stream<String> stream2 = Arrays.stream(arr, 1, 3);
stream2.forEach(e -> System.out.print(e + " "));
```

### ✔️ 결과

```
넷 둘 셋 하나
둘 셋
```

## 📌 3. 가변 매개변수

- Stream 클래스의 of() 메소드를 사용하면 가변 매개변수(variable parameter)를 전달받아 스트림을 생성할 수 있습니다.

### ✔️ 예시

```java
// 가변 매개변수에서 스트림 생성
Stream<Double> stream = Stream.of(4.2, 2.5, 3.1, 1.9);
stream.forEach(System.out::println);
```

### ✔️ 결과

```
4.2
2.5
3.1
1.9
```

## 📌 4. 지정된 범위의 연속된 정수

- IntStream이나 LongStream 인터페이스에는 range()와 rangeClosed() 메소드가 정의되어 있음.
- range()메소드는 시작 정수를 포함하지만 마지막 정수는 포함하지 않음.
- rangeClosed()메소드는 시작 정수와 마지막 정수까지도 포함하는 스트림 생성.

### ✔️ 예시

```java
// 지정된 범위의 연속된 정수에서 스트림 생성
IntStream stream1 = IntStream.range(1, 4);
stream1.forEach(e -> System.out.print(e + " "));
System.out.println();

IntStream stream2 = IntStream.rangeClosed(1, 4);
stream2.forEach(e -> System.out.print(e + " "));
```

### ✔️ 결과

```
1 2 3
1 2 3 4
```

## 📌 5. 특정 타입의 난수들

- 특정 타입의 난수로 이루어진 스트림을 생성하기 위해 Random 클래스에는 ints(), longs(), doubles()와 같은 메소드가 정의되어 있음
- 매개변수로 long타입으로 전달받을 수 있음.
- 매개변수를 전달하지 않으면 크기가 정해지지 않은 무한 스트림을 반환하므로 limit() 메소드를 사용하여 따로 스트림의 크기를 제한해야함.

### ✔️ 예시

```java
// 특정 타입의 난수로 이루어진 스트림 생성
IntStream stream = new Random().ints(4);
stream.forEach(System.out::println);
```

### ✔️ 결과

```
1072176871
-649065206
133298431
-616174137
```

## 📌 6. 람다 표현식

- 람다 표현식을 매개변수로 전달받아 해당 람다 표현식에 의해 반환되는 값을 요소로 하는 무한 스트림을 생성하기 위해 Stream 클래스에는 iterate()와 generate() 메소드가 정의되어 있음

### ✔️ 예시

```java
//iterate() 메소드를 이용하여 2부터 짝수 10개 생성
IntStream stream = Stream.iterate(2, n -> n + 2).limit(10);
```

## 📌 7. 파일

- 파일의 한 행(line)을 요소로 하는 스트림을 생성하기 위해 java.nio.file.Files 클래스에는 lines() 메소드가 정의되어 있음
- java.io.BufferedReader 클래스의 lines() 메소드를 사용하면 파일뿐만 아니라 다른 입력으로부터도 데이터를 행(line) 단위로 읽어 올 수 있음

### ✔️ 예시

```java
String<String> stream = Files.lines(Path path);
```

## 📌 8. 빈 스트림

- 아무 요소도 가지지 않는 빈 스트림은 Stream 클래스의 empty() 메소드를 사용하여 생성 가능

### ✔️ 예시

```java
// 빈 스트림 생성
Stream<Object> stream = Stream.empty();
System.out.println(stream.count()); // 스트림의 요소의 총 개수를 출력함.
```

### ✔️ 결과

```
0
```

# ✨스트림 연산✨

## 📌 스트림의 중개 연산

- 초기 스트림은 중개 연산을 통해 또 다른 스트림으로 변환되고 이러한 중개 연산은 스트림을 전달받아 스트림을 반환하므로, 중개 연산은 연속으로 연결해서 사용할 수 있음.

### ✔️ 1. 스트림 필터링

- `filter()`: 해당 스트림에서 주어진 조건에 맞는 요소만으로 구성된 새로운 스트림 반환
- `distinct()` : 내부적으로 Object 클래스의 equals() 메소드를 사용하여 요소의 중복 비교하고 중복 요소 제거

### ✔️ 2. 스트림 변환

- `map()` : 해당 스트림의 요소들을 주어진 함수에 인수로 전달하여, 그 반환값들로 이루어진 새로운 스트림 반환
- `flatMap()` : 각 배열의 각 요소의 반환값을 하나로 합친 새로운 스트림을 얻을 수 있음.

### ✔️ 3. 스트림 제한

- `limit()` : 첫 번째 요소부터 전달된 개수만큼의 요소만으로 이루어진 새로운 스트림 반환
- `skip()` : 첫 번째 요소부터 잔달된 개수만큼의 요소를 제외한 나머지 요소만으로 이루어진 새로운 스트림 반환

### ✔️ 4. 스트림 정렬

- `sorted()` : 주어진 비교자를 이용하여 정렬하고 비교자를 전달하지 않으면 기본적으로 오름차순 정렬

### ✔️ 5. 스트림 연산 결과 확인

- `peek()` : 원본 스트림에서 요소를 소모하지 않고, 연산과 연산 사이에 결과를 확인하고 싶을 때 사용.

## 📌 스트림의 최종 연산

- 중개 연산을 통해 변환된 스트림은 마지막으로 최종 연산을 통해 각 요소를 소모하여 결과 표시.
- 지연 되었던 모든 중개 연산들이 최종 연산에서 모두 수행되고 해당 스트림은 더 이상 사용할 수 없음.

### ✔️ 1. 요소의 출력

- `forEach()` : 스트림의 각 요소를 소모하여 명시된 동작 수행. 반환 타입이 void 이므로 스트림의 모든 요소를 출력하는 용도로 많이 사용

### ✔️ 2. 요소의 소모

- `reduce()` : 첫 번째와 두 번째 요소를 가지고 연산을 수행한 뒤, 그 결과와 세변째 요소를 가지고 또다시 연산 수행. 인수로 초기값을 전달하면 초기값과 해당 스트림의 첫 번째 요소로 연산을 시작하며, 그 결과와 두 번째 요소를 가지고 걔속해서 연산 수행

### ✔️ 3. 요소의 검색

- `findFirst()`, `findAny()` : 첫 번째 요소를 참조하는 Optional 객체 반환. 병렬 스트림일 경우 findAny() 메소드를 사용해야만 정확한 연산 결과 반환.

### ✔️ 4. 요소의 검사

- `anyMatch()` : 해당 스트림의 일부 요소가 특정 조건을 만족할 경우 true 반환
- `allMatch()` : 해당 스트림의 모든 요소가 특정 조건을 만족할 경우 true 반환
- `noneMatch()` : 해당 스트림의 모든 요소가 특정 조건을 만족하지 않을 경우 true 반환

### ✔️ 5. 요소의 통계

- `count()` : 해당 스트림의 요소의 총 개수를 long타입으로 반환
- `max()`, `min()` : 해당 스트림의 요소 중 가장 큰 값과 가장 작은 값을 참조하는 Optional 객체 반환

### ✔️ 6. 요소의 연산

- `sum()`, `average()` : IntStream, DoubleStream과 같은 기본 타입 스트림에는 모든 요소에 대해 합과 평균을 구할 수 있는 메소드가 정의되어 있음. average() 메소드는 각 기본 타입으로 래핑 된 Optional 객체 반환

### ✔️ 7. 요소의 수집

- `collect()` 메소드는 인수로 전달되는 Collectors 객체에 구현된 방법대로 스트림의 요소를 수집

  - 스트림을 배열이나 컬렉션으로 변환 : `toArray()`, `toCollection()`, `toList()`, `toSet()`, `toMap()`
  - 요소의 통계와 연산 메소드와 같은 동작 수행 : `counting()`, `maxBy()`, `minBy()`, `summingInt()`, `averagingInt()` 등
  - 요소의 소모와 같은 동작을 수행 : `reducing()`, `joining()`
  - 요소의 그룹화와 분할 : `groupingBy()`, `partitioningBy()`
