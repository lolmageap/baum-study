# 동시성을 제어하는 방법
Kotlin의 @Synchronized 어노테이션을 사용하면 특정 메소드가 한 번에 하나의 스레드에 의해서만 실행되도록 보장할 수 있으며, 이는 내부적으로 자바의 synchronized 키워드를 사용하여 구현됩니다.  
@Synchronized를 사용하면 자원의 동시 접근을 제어하여 데이터의 일관성과 안정성을 유지할 수 있습니다.

</br>

```kotlin
import kotlin.concurrent.thread

class Counter {
  private var value = 0
  @Synchronized fun add(value: Int) {
    this.value += value
    println(this.value)
  }
}

fun main() {
  val counter = Counter()
  for (i in 1..5) {
    thread {
      counter.add(i)
    }
  }
}
```
Counter 클래스의 add 메소드에 @Synchronized 어노테이션을 사용하여, 여러 스레드가 동시에 add 메소드를 호출하려고 할 때 한 번에 하나의 스레드만 해당 메소드를 실행할 수 있도록 보장합니다.
