# AutoBoxing
 
- 원시 타입의 값을 해당하는 wrapper 클래스의 객체로 바꾸는 과정을 의미


자바 컴파일러는 원시 타입이 아래 두 가지 경우에 해당될 때 autoBoxing을 적용한다.

- 원시타입이 Wrapper 클래스의 타입의 파라미터를 받는 메서드를 통과할 때

- 원시 타입이 Wrapper 클래스의 변수로 할당될 때


return 하는 text의 타입은 원시 타입이지만 해당 메서드의 리턴 타입은 Integer이다. (Wrapper 클래스 타입)

```java
public class Test {
private int text;

public Integer getText() {
    return text;
}
```

해당 결과를 실행하면 컴파일 오류가 발생하지 않는다.

왜일까?

 

자바 컴파일러가 자동으로 Integer 값으로 바꾸어주기 때문이다.

원래는 아래와 같은 과정이 숨어있다.

```java
public class Test {
private int text;

public Integer getText() {
    return Integer.valueOf(text);
}
```


# UnBoxing
- Wrapper 클래스 타입을 원시 타입으로 변환하는 과정의 의미

ex) Integer -> int

 

자바 컴파일러는 원시타입이 아래 두 가지 경우에 해당될 때 unBoxing을 적용한다.

 

- Wrapper 클래스 타입이 원시 타입의 파라미터를 받는 메서드를 통과할 때
- Wrapper 클래스 타입이 원시 타입의 변수로 할당될 때
```java
// Java program to illustrate the concept 
// of Autoboxing and Unboxing 
import java.io.*; 
  
class GFG 
{ 
    public static void main (String[] args) 
    { 
        // creating an Integer Object 
        // with value 10. 
        Integer i = new Integer(10); 
  
        // unboxing the Object 
        int i1 = i; 
  
        System.out.println("Value of i: " + i); 
        System.out.println("Value of i1: " + i1); 
  
        //Autoboxing of char 
        Character gfg = 'a'; 
  
        // Auto-unboxing of Character 
        char ch = gfg; 
        System.out.println("Value of ch: " + ch); 
        System.out.println("Value of gfg: " + gfg); 
  
    } 
} 
```

## Autoboxing / Unboxing 장점


- Autoboxing과 unBoxing은 개발자에게 가독성이 높고 깨끗한 코드를 작성하는데 도움을 준다.

- Autoboxing과 unBoxing을 사용한다면 Wrapper클래스 타입과 원시 타입을 상호 교환 가능하게끔 사용할 수 있고

- 명시적으로 타입 캐스팅을 수행하지 않아도 된다.

### Reference
- [주누의 개발일지](https://jwdeveloper.tistory.com/148)