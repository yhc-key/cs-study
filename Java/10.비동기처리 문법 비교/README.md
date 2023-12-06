
## 동기와 비동기
- `동기 (sync)` : A라는 작업이 끝나는 동시에 B라는 작업을 시작한다.

- `비동기 (async)` : A라는 작업의 완료 여부와 상관없이 B라는 작업을 시작한다.

비동기 방식은 작업들이 서로의 작업 시작 및 종료 시간에 영향을 받지 않고, 별도의 작업 시작/종료 시간을 가진다.

모든 비동기 방식은 멀티 스레드에서 작동한다.

- 동기 코드
    - 작업1과 작업2는 서로 연관이 없고 작업2가 더 빨리 끝나지만 비동기이므로 작업1이 끝난 후 작업2가 시작된다.
```java
public class AsyncExample{
    public static void main(String[] args){
        //작업1 시작
        try{
            Thread.sleep(1000);
        } catch(InterruptedException e){
            e.printStackTrace();
        }
        //작업1 종료

        //작업2 시작
        try{
            Thread.sleep(500);
        }catch(InterruptedException e){
            e.printStackTrace();
        }
        //작업2 종료
    }
}

```

- 비동기 코드
    - 실행시간이 긴 작업1을 다른 스레드에게 맡기고, 메인 스레드는 밑으로 내려가 작업2를 시작한다.

```java
public class AsyncExample{
    public static void main(String[] args){
        ExecutorService executorService = Executors.newCachedThreadPool();

        //작업1
        executorService.submit(() -> {
            try{
                Thread.sleep(1000);
            } catch(InterruptedException e){
                e.printStackTrace();
            }
        });
        

        //작업2 -> 이거 먼저 실행됨
        try{
            Thread.sleep(500);
        }catch(InterruptedException e){
            e.printStackTrace();
        }
        
        executorService.shutdown();
    }
}

```


## 자바에서 비동기를 처리하는 방법
## 1. Callback
다른 주체에게 맡긴 작업이 끝나면 다른 주체 쪽에서 본 주체가 전해 준 콜백 함수를 실행하는 방법

### CompletionHandler
비동기 I/O 작업의 결과를 처리하기 위한 목적으로 만들어 졌으며, 콜백 객체를 만드는데 사용된다. completed() 메소드를 오버라이드해서 콜백을 구현하고, failed() 메소드를 오버라이드해 작업이 실패했을 때의 처리를 구현한다.


```java
public class AsyncExample{
    //CompletionHandler
    private static final CompletionHandler<String, Void> completionHandler = new CompletionHandler<>(){
        //작업1이 성공적으로 종료된 경우 불리는 콜백 (작업2)
        @Override
        public void completed(String result, Void attachment){
            try{
            Thread.sleep(1000);
            }catch(InterruptedException e){
                e.printStackTrace();
            }
        }
        //작업2 종료

        //작업1이 실패했을 경우 불리는 콜백
        @Override
        public void failed(Throwable exc, Void attachment){
            System.out.println("작업1 실패: "+exc.toString());
        }
    };

    public static void main(String[] args){
        ExecutorService executorService = Executors.newCachedThreadPool();

        //작업1 시작
        executorService.submit(() -> {
            try{
                Thread.sleep(1000);
            } catch(InterruptedException e){
                e.printStackTrace();
            }
            //작업1 종료


            String result = "finished";
            if(result.equals("finished")){
                //작업성공
                completionHandler.completed(result, null);
            }else{
                //작업실패
                completionHandler.failed(new IllegalStateException(), null);
            }
        });

        //별개로 돌아가는 작업3
        try{
            Thread.sleep(1500);
        }catch(InterruptedException e){
            e.printStackTrace();
        }
        //작업3 종료
    }
}


작업3 시작 → 작업1 시작 → 작업2 시작 (작업1이 성공하고 completed()메서드 실행) → 작업3 종료 → 작업 2 종료

## 함수형 인터페이스
```java
public class AsyncExample{
    private static ExecutorService executorService;

    public static void main(String[] args){

        executorService = Executors.newCachedThreadPool();

        execute(parameter -> {
            log("작업 2 시작 (작업1의 결과 : " + parameter + ")");
            try{
            Thread.sleep(1000);
            }catch(InterruptedException e){
                e.printStackTrace();
            }
            log("작업2 종료");
        });

        //별개로 돌아가는 작업 3
        log("작업 3 시작");
        try{
            Thread.sleep(1500);
        }catch(InterruptedException e){
            e.printStackTrace();
        }
        log("작업3 종료");
    }

    public static void execute(Consumer<String> callback){
        executorService.submit(() -> {
            log("작업1 시작");
            try{
                Thread.sleep(1000);
            } catch(InterruptedException e){
                e.printStackTrace();
            }
          
            String result = "finished";
            log("작업1 종료");

            callback.accept(result);
        });
    }

    private static void log(String content){
        System.out.println(Thread.currentThread().getName() + "> " +content);
    }
}

```

작업1을 마친 뒤 callback으로 받아온 함수형 인터페이스를 싱행하는 메소드를 호출한다.

execute()의 인자로 execute()의 작업이 모두 끝난 뒤 실행 될 콜백을 작성했다.

## 2. Future
다른 주체에게 작업을 맡긴 상태에서 본 주체 쪽에서 작업이 끝났는지 직접 확인

```java
public class AsyncExample{
    public static void main(String[] args){
        ExecutorService executorService = Executors.newCachedThreadPool();

        Future<String> future = executorService.submit(() -> {
            System.out.println("작업1 시작");
            Thread.sleep(1000);
            System.out.println("작업1 종료");
            return "finished";

        });

        System.out.println("작업2 시작");
        String result = "";

        try{
            // 논 블로킹으로 작업1이 종료되었는지 확인
            System.out.println("작업1 종료 여부: "+future.isDone());
            //블로킹 상태에서 작업 1이 끝날때까지 대기
            result = future.get();
            System.out.println("작업1 종료 여부: "+ future.isDone());
        } catch(InterruptedException | ExecutionException e){
            e.printStackTrace();
        }
        System.out.println("작업 1 결과: "+result);
        System.out.println("작업 2 종료");
    }

}

```

확인하는 방법은 두 가지로 하나는 `isDone()`이나 `isCanceled()` 메소드로 블로킹 없이 작업을 완료했는지의 여부만 확인하는 방법이고, 다른 하나는 `get()`으로 작업이 완료될 때까지 블로킹된 상태로 대기하는 방법이다.

오래 걸리는 작업을 다른 주체에게 맡겨 두고 `get()`을 호출하기 전까지 이 쪽에서 할 일을 하다가, 작업을 마치면 `get()`을 호출해 작업의 결과를 받아오는 식으로 사용한다.

- 실행결과
```
작업2 시작
작업1 시작
작업1 종료 여부: false
작업1 종료
작업1 종료 여부: true
작업1 결과finished
작업2 종료
```

### CompletableFuture
Future는 다른 주체의 결과를 얻으려면 잠시라도 블로킹 상태에 들어갈 수 밖에 없는 한계가 있어 등잔한게 CompletableFuture이다.

```java
public class AsyncExample{
    public static void main(String[] args){
        new Thread(() ->{
            try{
                CompletableFuture
                    .supplyAsync(AsyncExample::work1)
                    .thenAccept(AsyncExample::work2)
                    .get();
            } catch(InterruptedException | ExecutionException e){
                e.printStackTrace();
            }
        }).start();

        work3();
    }

    private static String work1(){
        System.out.println("작업1 시작");

        System.out.println("작업1 종료");
    }

    private static String work2(){
        System.out.println("작업1 결과물: "+result);
        System.out.println("작업2 시작");
        try{
            Thread.sleep(1000);
        } catch(InterruptedException e){
            e.printStackTrace();
        }
        System.out.println("작업2 종료");
        
    }

    private static String work3(){
        System.out.println("작업3 시작");
        try{
            Thread.sleep(1000);
        } catch(InterruptedException e){
            e.printStackTrace();
        }
        System.out.println("작업3 종료");
    }
}

```

작업3 시작 -> 작업1 시작 -> 작업1 종료 -> 작업1 결과물: hash -> 작업2 시작 -> 작업3 종료 -> 작업2 종료

스레드 생성 후 작업1을 supplyAsync()를 통해 호출하고, 작업2를 작업1이 끝난 직후 블로킹 없이 시작할 수 있도록 thenAccept()를 통해 호출하고 있다. 그 밑에는 main스레드에서 작업3을 불러 시작하는 것도 볼 수 있다. 이렇게 CompletableFuture를 사용하면 이전 작업의 결과를 get()을 사용해 블로킹으로 가져올 필요 없이, then()함수를 통해 논블로킹을 유지하며 바로 사용할 수 있다.