# code level 에서 동시성을 제어하는 방법

만약 돈을 공유 자원으로 사용하게 된다면 동시성 문제가 발생할 수 있습니다.  
이를 해결하기 위해 코드 레벨에서 동시성을 제어하는 방법을 알아보겠습니다.  

```kotlin
object Money {
    var value = 1000

    operator fun plusAssign(money: Int) {
        this.value += money
    }

    operator fun minusAssign(money: Int) {
        this.value -= money
    }
}
```

## 동시성을 제어하지 않은 코드

아래를 실행하면 money의 값이 2000이 아닌 1588이 나옵니다.  
이는 여러 스레드에서 동시에 money의 값을 변경하기 때문에 발생하는 문제입니다.  
운영체제가 없다면 이러한 프로세스들의 동시성을 제어할 수 없습니다.  

```kotlin
object Money {
    var value = 1000

    operator fun plusAssign(money: Int) {
        this.value += money
    }

    operator fun minusAssign(money: Int) {
        this.value -= money
    }
}

fun main() {
    val money = Money
    val thread1 = Thread {
        for (i in 1..500) {
            money.value += 1
        }
    }

    val thread2 = Thread {
        for (i in 1..500) {
            money.value += 1
        }
    }
    
    thread1.start()
    thread2.start()
    
    thread1.join()
    thread2.join()
    
    require(money.value != 2000)
}
```

## 동시성 제어 로직

`synchronized` 키워드를 사용하면 동시성을 제어 할 수 있습니다.

```kotlin
object Money {
    var value = 1000

    operator fun plusAssign(money: Int) {
        synchronized(this) {
            this.value += money
        }
    }

    operator fun minusAssign(money: Int) {
        synchronized(this) {
            this.value -= money
        }
    }
}

fun main() {
    val money = Money
    val thread1 = Thread {
        for (i in 1..500) {
            money.value += 1
        }
    }

    val thread2 = Thread {
        for (i in 1..500) {
            money.value += 1
        }
    }


    thread1.start()
    thread2.start()
    thread1.join()
    thread2.join()

    require(money.value == 2000)
}
```

## scale out 시 동시성 제어

`synchronized` 키워드는 하나의 프로세스에서만 동작합니다.  
여러 서버에서 동시성을 제어하려면 `Lock`을 사용해야 합니다.  
아래는 redis를 사용한 spin lock 예시입니다.  

```kotlin
object Money {
    var value = 1000

    operator fun plusAssign(money: Int) {
        this.value += money
    }

    operator fun minusAssign(money: Int) {
        this.value -= money
    }
}

fun spinLock(key: String) {
    while (
        isLock(key) == false
    ) {
        Thread.sleep(100)
    }

    try {
        Money.value += 1
    } finally {
        redisTemplate.delete(redisKey)
    }
}


private fun isLock(key: String) = redisTemplate.opsForValue()
    .setIfAbsent(key, "lock", Duration.ofSeconds(5))


fun main() {
    val money = Money
    val thread1 = Thread {
        for (i in 1..500) {
            spinLock(UUID.randomUUID().toString())
        }
    }

    val thread2 = Thread {
        for (i in 1..500) {
            spinLock(UUID.randomUUID().toString())
        }
    }

    thread1.start()
    thread2.start()
    
    thread1.join()
    thread2.join()

    require(money.value == 2000)
}
```