# 16. 리플렉션(Reflection)

# ✨리플렉션(Reflection) 개념✨

> 구체적인 클래스 타입을 알지 못하더라도 그 클래스의 메서드, 타입, 변수들에 접근할 수 있도록 해주는 자바 API
>
> 컴파일 시간이 아닌 실행 시간에 동적으로 특정 클래스의 정보를 추출할 수 있는 프로그래밍 기법

자바 세상 어디에 반사?

- Refelction 사전적 의미: 반사
- 실쳬: Class, 거울: JVM 메모리 영역
- JVM은 클래스 정보를 클래스 로더를 통해 읽어와서 해당 정보를 JVM 메모리에 저장
- 힙 영역에 로드 된 Class 타입의 객체를 통해, 원하는 클래스의 인스턴스를 생성할 수 있도록 지원함

### ✔️ 리플렉션 사용 시점

- 동적으로 클래스를 사용해야할 때 필요
- 작성 시점에는 어떠한 클래스를 사용해야 할지 모르지만 런타임 시점에서 클래스를 가져와서 실행해야 하는 경우 필요
- 대표적으로 `Spring의 어노테이션 같은 기능`
  - 어떻게 실행시점에 빈 주입 가능?
  - 리플렉션을 이용하여 프로그램 실행 도중 동적으로 클래스의 정보를 가져와서 사용

# ✨리플렉션 사용✨

<소스코드 - Dog cLass>

```java
public class Dog extends Animal{
    private String myName = "뽀삐";
    public  String myCity = "서울";

    public Dog() {
    }

    private Dog(String myName) {
        this.myName = myName;
    }

    private void myName(String name){
        System.out.println("myName : " + name);
    }

    private void myCity(String city){
        System.out.println("myCity : " + city);
    }

    private void hello(){
        System.out.println("hello~");
    }
}
```

## 📌 **1. Class**

### ✔️ `class`

- class 정보 가져오기
- class가 무엇인지 알고 있을 때 사용 가능

```java
Class cls = Dog.class;
```

### ✔️ `getClass()`

- class 정보 가져오기
- 인스턴스의 getClass() 메소드 사용

```java
Dog 댕이 = new Dog("댕이");
Class cls = 댕이.getClass();
```

### ✔️ `forName()`

- class 정보 가져오기
- class 이름만으로 클래스의 정보를 가져옴
- 단, 패키지 네임이 포함된 클래스의 이름으로 작성해야 함

```java
Class cls = Class.forName("org.example.Dog");
```

## 📌 여기서 잠깐!

> Class 객체의 메소드 중 `getFields()` , `getMethods()` , `getAnnotations()` 와 같은 형태와 `getDeclaredFields()` , `getDeclaredMethods()` , `getDeclaredAnnotations()` 와 같은 형태로 메소드가 정의되어 있음
>
> 이 메소드들은 클래스에 정의된 필드, 메소드, 어노테이션 목록을 가져오기 위해 사용됨

- 이 두 형태를 각각 getXXX(), getDeclaredXXX()로 나눈다면

### ✔️ getXXX() vs getDeclaredXXX()

`getXXX()` : 상위 클래스와 상위 인터페이스에서 상속한 메서드를 포함하여 public인 메서드들을 모두 가져온다

`getDeclaredXXX()` : 접근 제어자와 관계 없이 상속한 메서드를 제외하고 직접 클래스에서 선언한 메서드들을 모두 가져온다

## 📌 **2. Constructor**

### ✔️ `getDeclaredConstructor()`

- 인자 없는 생성자 가져오기
- 기본 생성자가 없고 오버로딩된 생성자만 있다면 java.lang.NoSuchMethodException 예외를 발생

```java
Constructor constructor = cls.getDeclaredConstructor();
```

### ✔️ `getDeclaredConstructor(param)`

- 인자 있는 생성자 가져오기
- 인자를 넣으면 해당 타입과 일치하는 생성자 찾음

```java
Constructor constructor = cls.getDeclaredConstructor(String.class);
```

### ✔️ `getDeclaredConstructors()`

- 모든 생성자 가져오기
- private, public 등 모든 생성자 리턴

```java
Constructor constructors[] = cls.getDeclaredConstructors();
```

### ✔️ `getConstructors()`

- public 생성자만 가져오기

```java
Constructor constructors[] = cls.getConstructors();
```

### ✔️ `newInstance()`

- 가져온 생성자의 newInstance() 메소드를 사용하여 객체를 직접 생성 가능
- 타입 캐스팅을 하지 않으면 Object 타입으로 받아와짐

```java
Constructor constructor = cls.getDeclaredConstructor(String.class);

Dog dog = (Dog) constructor.newInstance("댕댕이");
```

### ✔️ `setAccessible(true)`

- private 생성자로 객체를 생성할 경우 나는 예외 해결 가능

```java
constructor.setAccessible(true);
Dog dog = (Dog) constructor.newInstance("댕댕이");
```

## 📌 **3. Method**

### ✔️ `getDeclaredMethod()`

- 인자가 없는 메서드 가져오기

```java
Method method = cls.getDeclaredMethod("hello", null);
```

### ✔️ `getDeclaredMethod(param)`

- 인자가 있는 메서드 가져오기

```java
Method method = cls.getDeclaredMethod("myName", String.class);
```

### ✔️ `getDeclaredMethods()`

- 모든 메서드 가져오기

```java
Method methods[] = cls.getDeclaredMethods();
```

### ✔️ `getMethods()`

- 상속받은 메서드와 public 메서드만 가져오기

```java
Method methods[] = cls.getMethods();
```

## 📌 **4. Field**

### ✔️ `getDeclaredField()`

- 전달된 이름과 일치하는 필드를 찾기

```java
Field field = cls.getDeclaredField("myName");
```

### ✔️ `getDeclaredFields()`

- 객체에 선언된 모든 필드를 찾기
- 단, 상속받은 객체의 정보는 찾아주지 않음

```java
 Field fields[] = cls.getDeclaredFields();
```

### ✔️ `getFields()`

- 객체에 선언된 모든 필드를 찾기
- 상속 받은 객체의 public 필드까지 찾아줌

```java
Field fields[] = cls.getFields();
```

### ✔️ `getField()`

- 클래스로부터 필드 정보를 가져옴
- 객체의 public 필드 찾아서 값 변경할 수 있음

```java
Dog dog = new Dog();
Class cls = Class.forName("test.Dog");
Field field = cls.getField("myCity");

System.out.println("default field : " + field.get(dog));
// default field : 서울 출력

field.set(dog, "제주도");
System.out.println("update field : " + field.get(dog));
// update field : 제주도 출력
```

- setAccessible() 사용하면 private로 선언한 필드 접근 가능

```java
Dog dog = new Dog();
Class cls = Class.forName("test.Dog");
Field field = cls.getDeclaredField("myName");
field.setAccessible(true);

System.out.println("default field : " + field.get(dog));
// default field : 뽀삐 출력

field.set(dog, "펩시");
System.out.println("update field : " + field.get(dog));
// update field : 펩시 출력

```

## 📌 **5. 어노테이션**

### ✔️ `getAnnotation()`

- 메소드에 직접 어노테이션 타입을 넣어주면, 클래스에 붙어 있는 어노테이션을 가져올 수 있음
- 어노테이션이 가지고 있는 필드에도 접근할 수 있는 것을 확인할 수 있음

```java
Class<Member> aClass = Member.class;

Entitiy entityAnnotation = aClass.getAnnotation(Entitiy.class);
String value = entityAnnotation.value();
System.out.println("value = " + value);
// 멤버
```

# ✨리플렉션 사용 이유✨

### ✔️ 동적인 객체 생성

Reflection을 사용하면 런타임 시에 클래스의 인스턴스를 동적으로 생성할 수 있습니다. 클래스 이름을 동적으로 전달하여 객체를 생성할 수 있으며, 이는 유연한 객체 생성 패턴을 구현하는 데 도움이 됩니다.

### ✔️ 클래스 정보 조사

Reflection은 클래스의 메서드, 필드, 상위 클래스, 인터페이스 등의 정보를 동적으로 조사할 수 있습니다. 이를 통해 프로그램이 실행 중에 클래스의 구조를 분석하고 다양한 작업을 수행할 수 있습니다. 예를 들어, 특정 어노테이션을 가진 메서드를 찾거나, 클래스의 필드 값을 변경할 수 있습니다.

### ✔️ 동적 메서드 호출

Reflection을 사용하면 메서드의 이름과 매개변수를 동적으로 지정하여 메서드를 호출할 수 있습니다. 이는 일반적으로 알려지지 않은 클래스나 인터페이스와 상호작용해야 할 때 유용합니다. 예를 들어, 외부 라이브러리나 플러그인 시스템과의 통합에 활용할 수 있습니다.

### ✔️ 어노테이션 처리

Reflection을 사용하면 어노테이션 정보를 읽고 처리할 수 있습니다. 런타임 시에 어노테이션을 분석하여 특정 작업을 수행하거나, 어노테이션을 기반으로 동적으로 코드를 생성할 수 있습니다. 이는 프레임워크나 라이브러리에서 커스텀 어노테이션을 활용하여 기능을 확장하거나, 실행 중에 설정 정보를 처리하는 데 사용됩니다.

### ✔️ 리소스 접근

Reflection을 사용하면 클래스로더를 통해 리소스에 접근할 수 있습니다. 클래스 경로에 있는 파일이나 설정 파일 등을 동적으로 로드하여 사용할 수 있습니다

# ✨주의 사항✨

1. Reflection은 강력한 기능이지만, 실행 시간에 타입 검사를 회피하고 퍼포먼스 저하를 일으킬 수 있다

2. 코드의 복잡성을 증가시킬 수 있으므로 신중하게 사용해야 한다

3. 주로 프레임워크, 라이브러리, 개발자 도구 등의 개발에 활용되며, 일반적인 애플리케이션 개발에서는 Reflection을 적게 사용하는 것이 좋다
