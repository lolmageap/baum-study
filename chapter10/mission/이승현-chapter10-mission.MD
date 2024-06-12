## 미션

- 문맥교환의 오버헤드를 줄일 수 있는 방법

## 스레드 풀 사용

- 스레드 : 프로세스를 구성하는 실행 흐름의 단위
- 문맥 교환이란 CPU가 한 스레드에서 다른 스레드로 전환할 때 필요한 작업입니다.
- 스레드가 많아지면 문맥 교환이 빈번해지고, 이는 시스템의 성능을 저하시키는 주요 원인 중 하나가 됩니다.
- 따라서 스레드 풀을 사용하여 스레드의 개수를 제한하고 재사용함으로써 문맥 교환의 오버헤드를 줄일 수 있습니다.

```java
public class ThreadPoolTest {
    public static void main(String[] args) {
        // 스레드 풀 생성 - 5개의 스레드로 구성된 풀을 생성
        ExecutorService executor = Executors.newFixedThreadPool(5);

        Runnable task = () -> {
            String threadName = Thread.currentThread().getName();
        };

        // 스레드 풀에 작업 제출
        for (int i = 0; i < 10; i++) {
            executor.submit(task);
        }

        executor.shutdown();
    }
}
```