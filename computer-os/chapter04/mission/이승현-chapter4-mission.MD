# Iterator 패턴

## 정의

- Iterator 패턴은 여러 개의 아이템(항목들)이 모여 있는 그룹에서 아이템을 하나씩 차례로 살펴볼 수 있게 해 주는 방법.

## 장단점

- 장점 : 그룹의 구조가 바뀌더라도 그룹 안의 아이템을 확인하는 방식을 바꾸지 않아도 됨. (독립성)
- 단점 : 규모가 작은 프로세스나 간단한 구조에서는 이 패턴을 사용하는 것이 오히려 무겁고 복잡해질 수 있음, 오버헤드 발생 가능성 있음.

```java
interface Iter {
    boolean hasNext();
    int next();
}
class Numbers {
    private int[] data;
    private int i = 0;

    public Numbers(int[] data) {
        this.data = data;
    }

    public boolean hasNext() {
        return i < data.length;
    }

    public int next() {
        return data[i++];
    }
}
public class Test {
    public static void main(String[] args) {
        Numbers nums = new Numbers(new int[]{1, 2, 3});
        while (nums.hasNext()) {
            System.out.println(nums.next());
        }
    }
}
```

# Observer 패턴

## 정의

- 한 객체의 상태변화를 관찰하고 있을 한 개 이상의 다른 객체에게 자동으로 알림을 제공함.

## 장단점

- 장점 : 발행자(이벤트를 알리는 객체)와 구독자(이벤트를 받는 객체) 간의 결합도가 낮아 유지보수 및 확정성이 용이함, 실시간으로 객체 간의 상태 변화를 전파할 수 있음.
- 단점 : 복잡한 의존성을 관리해야함, 메모리 누수 위험이 있음. (발행자가 구독자에 대한 참조를 계속 가지고 있어야 하는 경우 구독자들이 적절히 해체되지 않을 경우)

```java
class Event {
    private List<Observer> obs = new ArrayList<>();

    void add(Observer o) {
        obs.add(o);
    }

    void notify(String msg) {
        for (Observer o : obs) {
            o.update(msg);
        }
    }
}

interface Observer {
    void update(String msg);
}

class User implements Observer {
    public void update(String msg) {
        System.out.println(msg);
    }
}

public class Test {
    public static void main(String[] args) {
        Event e = new Event();
        e.add(new User());
        e.notify("이벤트가 발생했슈!");
    }
}

```