# 03. 전략 패턴

## ✨전략 패턴이란?✨

### 📌 정의

유사한 알고리즘들을 캡슐화해서 객체의 행위를 동적으로 변경 가능하게 만드는 패턴

= 실행(런타임) 중에 알고리즘 전략을 선택하여 객체 동작을 실시간으로 바뀌도록 할 수 있게 하는 행위 디자인 패턴

### 📌 장점

#### ✔️ 가독성, 유지보수성 좋아짐

전략 패턴의 경우

1. 어떤 한 로봇이 서 있음
   - 행동 인터페이스를 생성해서 행동 영역을 묶고, ‘서있다’ 객체 생성
   - 로봇 객체에 ‘서있다’ 객체를 넣어서 행동방식을 바꿈
2. 로봇에 감정을 추가
3. 뛰는 로봇이 행복해짐
   - 감정 인터페이스를 생성해서 감정 영역을 묶고, ‘행복한’ 객체 생성
   - 로봇 객체에 ‘행복한’ 객체를 넣어서 감정을 바꿈

이렇게 캡슐화 가능해서 행동/감정 변화에 따라 교체만 해주면 되는데, 전략 패턴을 사용하지 않으면 객체가 기하급수적으로 늘어남

#### ✔️ OCP(개방폐쇄의 원칙)

변경엔 닫혀 있고 확장엔 열려 있는 객체 지향 원칙이 실현

- 다른 행동 및 전략이 추가된다고 해도 기존의 코드는 변경하지 않고 확장이 가능

#### ✔️ 상속 대신 위임 사용 가능

상속은 단 한 개의 클래스만 상속할 수 있고, 상위 클래스가 바뀌면 하위 클래스도 모조리 영향을 받는다는 단점이 있음

반면 위임의 경우 해당 Context 클래스가 변경되어도 전략 영역은 전혀 영향 받지 않음

### 📌 단점

#### ✔️ 복잡성 증가

추가적인 클래스 및 인터페이스가 필요하기에 코드 복잡성이 증가될 수 있음

#### ✔️ 런타임 시에 알고리즘을 선택하는 데 추가적인 오버헤드 발생 가능

적절한 분석과 설계가 필요함

## ✨전략 패턴 예제✨

#### ✔️ 로봇 객체

```java
// 로봇
public class Robot {
    private MoveStrategy moveStrategy;
    private EmotionStrategy emotionStrategy;

    public Robot(MoveStrategy moveStrategy, EmotionStrategy emotionStrategy) {
        this.moveStrategy = moveStrategy;
        this.emotionStrategy = emotionStrategy;
    }

    public void robotExplain() {
        moveStrategy.move();
        emotionStrategy.emotion();
        System.out.println("로봇");
    }

    public void changeMoveStrategy(MoveStrategy moveStrategy) {
        this.moveStrategy = moveStrategy;
    }

    public void changeEmotionStrategy(EmotionStrategy emotionStrategy) {
        this.emotionStrategy = emotionStrategy;
    }
}

```

#### ✔️ 행동/감정 영역 설정

```java
// 행동 영역 설정
public interface MoveStrategy {
    void move();
}

// 감정 영역 설정
public interface EmotionStrategy {
    void emotion();
}

```

#### ✔️ 행동 객체

```java
// 서있는 행동 객체
public class Stand implements MoveStrategy {
    @Override
    public void move() {
        System.out.print("서있는 ");
    }
}

// 걷고있는 행동 객체
public class Walking implements MoveStrategy {
    @Override
    public void move() {
        System.out.print("걷고있는 ");
    }
}

// 달리는 행동 객체
public class Running implements MoveStrategy {
    @Override
    public void move() {
        System.out.print("달리는 ");
    }
}

```

#### ✔️ 감정 객체

```java
// 화난 감정 객체
public class Angry implements EmotionStrategy {

    @Override
    public void emotion() {
        System.out.print("화난 ");
    }
}

// 행복한 감정 객체
public class Happy implements EmotionStrategy {
    @Override
    public void emotion() {
        System.out.print("행복한 ");
    }
}

```

#### ✔️ 객체 코드 사용

```java
// 메인 메서드
public class Main {
    public static void main(String[] args) {
        Robot robot = new Robot(new Walking(), new Angry()); // 걷고 있는, 화난 객체 설정
        robot.robotExplain(); // 출력
        robot.changeMoveStrategy(new Running()); // 걷고 있는 -> 달리는 교체
        robot.robotExplain(); // 출력
        robot.changeEmotionStrategy(new Happy()); // 화난 -> 행복한 교체
        robot.changeMoveStrategy(new Stand()); // 달리는 -> 서있는 교체
        robot.robotExplain(); // 출력
    }
}


// 출력 결과

// 걷고있는 화난 로봇
// 달리는 화난 로봇
// 서있는 행복한 로봇

```
