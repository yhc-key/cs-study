- [Xms(Initial Java Heap Size)](#xmsinitial-java-heap-size)
- [Xmx(Maximum Java Heap Size)](#xmxmaximum-java-heap-size)
  - [Reference](#reference)

# Xms(Initial Java Heap Size)

- JVM이 시작될 때 할당하는 **최소 Heap 크기**를 지정함
- Xms 옵션은 `Xms<size>` 형식으로 사용되며, 여기서 `<size>`는 바이트 단위의 크기를 나타낸다.
- 예를 들어, `Xms256m`의 의미는 최소 256MB의 힙을 할당하도록 지정한다.

# Xmx(Maximum Java Heap Size)

- JVM이 사용할 수 있는 **최대 Heap 크기**를 지정함
- 애플리케이션의 메모리 요구 사항에 따라 힙 크기를 동적으로 조정하는 데 사용됨
- Xmx 옵션은 `-Xmx<size>` 형식으로 사용되며, Xms와 마찬가지로 `<size>`는 바이트 단위의 크기를 나타낸다.

### Reference

- [devSquad-study](https://github.com/devSquad-study)
