# 19. CountdownLatch vs CyclicBarrier

`CountdownLatch`와 `Cyclicbarrier`은 쓰레드를 관리해주는 concurrent 패키지 내 동기화 클래스

# ✨CountdownLatch✨

`CountdownLatch`는 어떤 쓰레드가 다른 쓰레드에서 작업이 완료될 때까지 기다릴 수 있도록 해주는 클래스

쓰레드를 N개 실행했을 때, 일정 개수의 쓰레드가 모두 끝날 때 까지 기다려야지만 다음으로 진행할 수 있거나 다른 쓰레드를 실행시킬 수 있는 경우 사용

## 📌 CountDownLatch 작동 원리

`Latch`의 사전적 의미는 `걸쇠`를 의미로 원하는 지점에서 `await()`메소드를 호출하여 코드의 실행을 중단시키고 다른 쓰레드들에서 원하는 횟수만큼 `countDown()` 메서드를 호출하여 코드가 진행되는 방식

    - 예를 들어 메인 쓰레드에서 5개의 쓰레드를 실행시키고 CountDownLatch 값을 3으로 설정
    - 각 쓰레드가 종료되는 시점에 countDown()을 호출
    - 3개의 쓰레드가 종료 될 때 메인 쓰레드가 다시 시작됨

![CountDownLatch](img/countdownlatch.png)

CountDownLatch는 다음과 같이 생성할 수 있다.

인자로 Latch의 숫자를 전달한다.

```java
CountDownLatch countDownLatch = new CountDownLatch(5);
```

다음과 같이 `countDown()`을 호출하면 Latch의 숫자가 1개씩 감소합니다.

```java
countDownLatch.countDown();
```

`await()`은 Latch의 숫자가 0이 될 때까지 기다리는 코드입니다.

```java
countDownLatch.await();
```

## 📌 예제

```java
public class LatchExampleTest {
    @DisplayName("await 호출 쓰레드는 대기한다.")
    @Test
    public void name() throws InterruptedException {
        //CountDownLatch 객체를 생성하고 초기 카운트를 4로 설정
        CountDownLatch latch = new CountDownLatch(4);

        //4개의 스레드를 생성하고 실행
        //각 스레드는 job 메서드를 통해 일정 시간 동안 대기한 후에 CountDownLatch의 카운트를 감소시킴
        new Thread(job(latch,1)).start();
        new Thread(job(latch,2)).start();
        new Thread(job(latch,3)).start();
        new Thread(job(latch,4)).start();
        System.out.println("=======Main Thread await========");

        //메인 스레드에서 latch.await()를 호출하여 모든 스레드가 도달할 때까지 대기
        latch.await();

        //모든 스레드가 도달한 후 출력
        System.out.println("=======Main Thread restart========");
    }

    //각 스레드에서 실행하는 메서드
    Runnable job(CountDownLatch latch, int num) {
        return () -> {
            try {
                //num*500 밀리초 동안 일시 중지(스레드마다 대기 시간 다름)
                Thread.sleep(num*500L);

                System.out.println("Thread Num : "+ num + "countDown");

                //CountDownLatch의 카운트를 감소시킴
                latch.countDown();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        };
    }
}
```

## 📌 실행 화면

![CountDownLatch 실행 화면](img/countdownlatch_result.PNG)

`await()` 를 통해 메인 쓰레드를 대기 상태로 바꾸고 다른 쓰레드를 실행을 하여 `countDown()`을 호출한다.

`await()`는 타임아웃을 인자로 받는데, 이 타임아웃을 설정할 시에는 해당 시간동안 기다리다가 아래 코드를 실행한다.

<br>

# ✨CyclicBarrier✨

`CountdownLatch`와 비슷하지만 다른 점은 다른 쓰레드가 전부 대기 상태가 되었을 때 모든 쓰레드의 대기 상태가 해제가 되고 재사용이 가능해진다.

즉, `CyclicBarrier`는 `CountdownLatch` 처럼 1이상의 인자를 count 값을 받는다. 그리고 각 쓰레드에서 `await()`를 호출하면 쓰레드는 대기상태로 들어간다.

`await()`가 count 값만큼 호출이 되면, 대기 상태로 있던 쓰레드는 대기 상태가 해제된다.

![CyclicBarrier](img/cyclicbarrier.png)

## 📌 예제

```java
public class BarrierExampleTest {

    @DisplayName("Barrier는 다른 스레드의 대기 상태를 기다린다.")
    @Test
    void name() throws BrokenBarrierException,InterruptedException{
        //CyclicBarrier 객체를 생성하고 파티(Party) 수를 6으로 설정
        //파티 수는 대기 중인 스레드의 수를 나타냄
        // 5개의 스레드와 메인 스레드가 대기하게 됨
        CyclicBarrier cyclicBarrier = new CyclicBarrier(6);

        //5개의 스레드를 생성하고 실행
        for (int i = 0 ; i < 5 ; i++) {
            //run 메서드를 통해 일정 시간 동안 대기한 후에 CyclicBarrier를 통과하여 계속 진행
            new Thread(run(cyclicBarrier,i+1)).start();
        }

        //파티 수가 충족될 때까지 메인 스레드가 대기
        cyclicBarrier.await();

        //모든 스레드가 도달하면 출력
        System.out.println("Main Thread End");
    }

    Runnable run(CyclicBarrier barrier,int num) {
        return () -> {
          try {
              //num*500 밀리초 동안 일시 중지
              Thread.sleep(num * 1000);
              System.out.println("Thread " + num + "await");

              barrier.await();

              //CyclicBarrier를 통과한 후 출력
              System.out.println("Thread " + num + "end");
          } catch (BrokenBarrierException | InterruptedException e) {
              e.printStackTrace();
          }
        };
    }
}
```

## 📌 실행 화면

![CyclicBarrier 실행 화면](img/cyclicbarrier_result.PNG)

# ✨CountDownLatch vs CyclicBarrier✨

- `CyclicBarrier`는 여러 쓰레드가 서로를 기다리고 `CountDownLatch`는 하나 또는 다수의 쓰레드가 작업이 완료될 때 까지 기다린다.

- `CyclicBarrier`는 모든 쓰레드가 도달하면 값이 초기화 되고 `CountDownLatch`는 초기화 되지 않는다. 따라서 `CountDownLatch`는 재사용 불가능하다.
