- [Call by Value \& Call by Reference](#call-by-value--call-by-reference)
  - [Call by Value](#call-by-value)
  - [Call by Reference](#call-by-reference)
    - [🤦‍♀️Java에는 Call by Reference 없다며;](#️java에는-call-by-reference-없다며)

# Call by Value & Call by Reference

- **Call by Value**
  - 함수의 인자를 전달할 때 **값**을 전달하는 방식
- **Call by Reference**
  - **주소**를 전달하는 방식

<aside>
💡 자바는 `Call by Value` 방식을 사용하고, `Call by Reference` 는 존재하지 않는다.

</aside>

## Call by Value

- 함수의 인자를 전달할 때 **값**을 전달하는 방식. 전달받은 값을 복사해서 처리한다. 전달받은 값을 변경해도 원본은 변경되지 않음.

```java
public class MyClass {
    public static void main(String args[]) {
      int num1 = 10;
      int num2 = 20;

      System.out.println("Before Call Method : num1 = " + num1 + ", num2 = " + num2);
// 10, 20

      ex_method(num1, num2);

      System.out.println("After Call Method : num1 = " + num1 + ", num2 = " + num2);
// 30, 40
    }

    public static void ex_method(int num1, int num2) {
        num1 = 30;
        num2 = 40;

        System.out.println("Inside Method : num1 = " + num1 + ", num2 = " + num2);
// 10, 20

    }
}
```

## Call by Reference

- 참조에 의한 호출. 전달받은 값을 직접 참조한다. 전달받은 값을 변경하면 원본도 같이 변경된다.

```java
public class MyClass {
    int value;

    MyClass(int value) {
        this.value = value;
    }

    public static void swap(MyClass x, MyClass y) {
        int temp = x.value;
        x.value = y.value;
        y.value = temp;

    }

    public static void main(String[] args) {

        MyClass num1 = new MyClass(10);
        MyClass num2 = new MyClass(20);


        System.out.println("Before Call Method : num1 = " + num1.value + ", num2 = " + num2.value);
//10,20

        swap(num1, num2);

        System.out.println("After Call Method : num1 = " + num1.value + ", num2 = " + num2.value);
//20,10

    }
}
```

### 🤦‍♀️Java에는 Call by Reference 없다며;

자바의 데이터형은 두가지다.

1. **Primitive type(기본형) :** Boolean type(boolean), Numeric Type(short, int, long, float, double, char)
   - stack 변수 안에 value 저장
2. **Reference Type(참조형) :** Class Type, Interface Type, Array Type, Enum Type, 기본형을 제외한 모든 것들
   - stack 변수 값에는 객체의 주소 값, 객체는 별도의 Heap 영역에 저장

```java
public class main
{
    public static void main(String[] args)
    {
        int var = 1;
        int[] arr = { 1 };

        // call by value
        add_value(var);
        System.out.println("primitive type : " + var); // 1 : 값 변화가 없음

        // call by reference
        add_reference(arr);
        System.out.println("reference type : " + arr[0]); // 101 : 값이 변화함
    }

    static void add_value(int var_arg) {
        var_arg += 100;
    }

    static void add_reference(int[] arr_arg) {
        arr_arg[0] += 100;
    }
}
```

![기본형은 원시값 저장. 참조형의 실제데이터는 heap에 저장됐고, stack에는 주소값 저장](Call%20by%20Value,%20Call%20by%20Reference%200c8bc194eafa4c358df0645d47491302/Untitled.png)

기본형은 원시값 저장. 참조형의 실제데이터는 heap에 저장됐고, stack에는 주소값 저장

![add_value의 var_arge가 바뀐거라 var은 멀쩡함](Call%20by%20Value,%20Call%20by%20Reference%200c8bc194eafa4c358df0645d47491302/Untitled%201.png)

add_value의 var_arge가 바뀐거라 var은 멀쩡함

![arr랑 arr_arg랑 주소값이 같음. 두 변수가 하나의 데이터를 동시에 참조함](Call%20by%20Value,%20Call%20by%20Reference%200c8bc194eafa4c358df0645d47491302/Untitled%202.png)

arr랑 arr_arg랑 주소값이 같음. 두 변수가 하나의 데이터를 동시에 참조함

⇒ 이것만 보면 Call by Reference잖아!!하게 됨. 그치만 Java는 개발자가 직접 메모리 주소에 접근하지 못하기 때문에 Call by Reference라는 개념이 존재하지 않는다.

- 즉, `call by value`로 동작하면서 단지 **원시값**이 복사되느냐 **주소값**이 복사되느냐 차이이다.
- `add_reference()` 호출 시 `arr` 의 주소값을 복사해서 새로운 변수를 선언한다. 각 변수는 다른 scope에 존재하기 때문에 두 변수가 완전히 같지 않다. 하지만, `Call by Reference` 는 **두 변수가 완전히 같아야 한다.**
