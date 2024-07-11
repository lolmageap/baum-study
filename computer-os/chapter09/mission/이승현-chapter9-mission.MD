# 💡 Chapter 9 미션

운영체제는 여러 프로세스를 관리하면서 교착 상태를 방지해줘요.
이런 동시성을 제어 할 수 있는 방법을 코드로 작성해볼까요?

---

# 교착 상태

- 두 개 이상의 프로세스가 서로 상대방의 자원을 기다리면서 아무 것도 하지 못하는 상태를 의미합니다.

```java
class Counter {
    private int count = 0;
    private final Lock lock = new ReentrantLock();

    public void increment() {
        lock.lock();
        try {
            count++;
        } finally {
            lock.unlock();
        }
    }
}
```