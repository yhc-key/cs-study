- [Object-Oriented Programming(객체 지향 프로그래밍)](#object-oriented-programming객체-지향-프로그래밍)
  - [Object(객체)](#object객체)
  - [OOP의 4가지 특성](#oop의-4가지-특성)
    - [Abstraction(추상화)](#abstraction추상화)
    - [Inheritance(상속)](#inheritance상속)
    - [Polymorphism(다형성)](#polymorphism다형성)
    - [Encapsulation(캡슐화)](#encapsulation캡슐화)

# Object-Oriented Programming(객체 지향 프로그래밍)

컴퓨터 프로그램을 명령어의 목록으로 보는 시각 대신 여러 개의 독립된 단위인 객체의 모임으로 파악하는 것이다. 프로그래밍에서 필요한 데이터를 추상화시켜 상태와 행위를 가진 객체로 만든 후 객체들의 유기적인 결합과 협력으로 파악하고자 하는 컴퓨터 프로그래밍의 패러다임을 의미한다.

## Object(객체)

상태(state)와 행위(behavior)을 지닌 기본적인 단위. OOP에서 객체는 변수, 함수, 자료 구조의 조합이 될 수 있다.

## OOP의 4가지 특성

### Abstraction(추상화)

객체의 공통적인 속성과 기능을 추출해 정의하는 것. Interface를 사용한다.

### Inheritance(상속)

기존의 클래스를 재활용해 새로운 클래스를 작성하는 자바 문법 요소.

- **인터페이스를 통한 구현과 클래스들 간의 상속의 차이**
  - 인터페이스 : 인터페이스 내 정의된 추상 메서드 내용이 하위 클래스에서 정의되어야 한다.
  - 클래스들 간의 상속 : 오버라이딩을 사용해 메서드를 재정의하는 것이 가능하다.

### Polymorphism(다형성)

어떤 객체의 속성이나 기능이 상황에 따라 여러 가지 형태를 가질 수 있는 성질. OOP에서 다형성은 한 타입의 참조변수를 통해 여러 타입의 객체를 참조할 수 있게 만든 것을 의미힌다.

```groovy
public class Main {
    public static void main(String[] args) {
        //원래 사용했던 객체 생성 방식
        Phone phone = new Phone();
        Galaxy galaxy = new Galaxy();

        //다형성을 활용한 객체 생성 방식
        Phone galaxy = new Galaxy();
    }
}
```

### Encapsulation(캡슐화)

클래스 안에 서로 연관있는 속성과 기능들을 하나의 캡슐로 만들어 외부로부터 보호하는 것을 의미.

- OOP에서 캡슐화를 하는 이유
  - 데이터 보호 : 외부로부터 클래스에 정의된 속성과 기능들을 보호
  - 데이터 은닉 : 내부의 동작을 감추고 외부에는 필요한 부분만 노출
  ![Untitled](OOP%205c9fe4200db040daa99b378e35063637/Untitled.png)
