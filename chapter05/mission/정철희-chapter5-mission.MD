## 병렬 처리

병렬 처리란 여러 개의 작업을 동시에 처리하는 것을 의미합니다.  
병렬 처리를 통해 작업을 빠르게 처리할 수 있습니다.  
kotlin 에서는 병렬 처리를 위해 `Thread` 클래스를 사용할 수 있습니다.

```kotlin
fun main() {
    val thread = Thread {
        for (i in 1..5) {
            println(i)
        }
    }
  
    thread.start()
    thread.join()
}
```

위 코드는 1부터 5까지 출력하는 코드입니다.
총 5개의 작업을 5개의 스레드로 나누어 병렬로 처리하고 있습니다.

### 단점 
병렬 처리의 단점은 Thread의 생성과 파괴에 대한 오버헤드가 많이 발생합니다.
Thread를 생성하고 파괴하는데 발생하는 오버헤드가 정말 크기 때문에 대용량의 데이터를 처리할 때를 제외하고는 Thread를 새로 생성해서 사용하지 않는 것이 좋습니다.

### 극복 방법
위와 같이 `Thread` 클래스를 사용하여 병렬 처리를 할 수 있지만 `Coroutine`을 사용하면 더 간단하게 병렬 처리를 할 수 있습니다.
```kotlin
fun main() = runBlocking {
    val job = repeat(5) {
        launch {
            println(it)
        }
    }

    job.join()
}
```

위와 같이 코루틴을 사용하면 `Thread`를 새로 만들지 않고 다른 스레드에게 작업을 위임하기 때문에 오버헤드가 적습니다.