### 상황

게임에서 여러 플레이어가 동시에 접속하여 위치 데이터를 주고받는 상황으로, 여러 스레드가 동시에 접근할 때 데이터 일관성을 유지해야 하는 상황입니다.

```java
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

public class Main {
    public static void main(String[] args) {
        // ConcurrentHashMap 생성: 플레이어들의 위치를 저장하는 데 사용
        ConcurrentHashMap<String, Integer> playerPositions = new ConcurrentHashMap<>();

        // ExecutorService 생성: 두 개의 스레드를 관리하는 스레드 풀 생성
        ExecutorService service = Executors.newFixedThreadPool(2);

        // 초기 플레이어 위치 설정
        playerPositions.put("player1", 0);
        playerPositions.put("player2", 0);

        // player1의 위치를 업데이트하는 스레드
        service.submit(() -> {
            for (int i = 0; i < 100; i++) {
                playerPositions.put("player1", i);
                try {
                    Thread.sleep(10);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        });

        // player2의 위치를 업데이트하는 스레드
        service.submit(() -> {
            for (int i = 0; i < 100; i++) {
                playerPositions.put("player2", i);
                try {
                    Thread.sleep(10); // 10ms 동안 대기
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        });

        // 더 이상 새로운 작업을 받지 않도록 서비스 종료
        service.shutdown();
        try {
            service.awaitTermination(1, TimeUnit.MINUTES);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        // 모든 플레이어의 최종 위치 출력
        playerPositions.forEach((player, position) ->
            System.out.println(player + " final position: " + position));
    }
}

// 예상 출력 결과:
// player1 final position: 99
// player2 final position: 99
```

### **ConcurrentHashMap**

- 여러 스레드가 동시에 데이터를 읽고 쓸 수 있도록 설계된 맵입니다.
- 세분화된 잠금을 사용해 성능을 최적화하며, `synchronized` 블록 없이도 데이터 일관성을 유지합니다.
- 다중 스레드 환경에서 안전하게 사용할 수 있습니다.

**ExecutorService와 Executors**

- `ExecutorService`는 비동기 작업을 관리하고 실행하기 위한 프레임워크입니다.
- `Executors` 클래스는 다양한 스레드 풀을 생성할 수 있는 정적 메서드를 제공합니다.
- 스레드 풀을 사용하면 스레드를 효율적으로 관리하고 재사용할 수 있습니다.
