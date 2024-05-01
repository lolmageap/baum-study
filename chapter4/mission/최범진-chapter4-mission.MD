# 시나리오

스터디 카페에 손님들에게 영업 종료후 퇴실 알람을 보내보자

# Iterator 패턴

컬렉션의 요소들을 순차적으로 접근하는 것을 추상화 하는 패턴

```java
public class Cafe {
    public static void main(String[] args) {
        List<Customer> customers = new ArrayList<>();
        for (int i = 0; i < 100; i++) {
            customers.add(new Customer("Customer " + i));
        }

        Iterator<Customer> iterator = customers.iterator();
        while (iterator.hasNext()) {
            Customer customer = iterator.next();
            customer.exitCafe();
        }
    }
}

class Customer {
    private String name;

    public Customer(String name) {
        this.name = name;
    }

    public void exitCafe() {
        System.out.println(name + " 퇴실 시간입니다.");
    }
}
```

# Observer 패턴

상태의 변화를 관찰하고, 변화가 있을 때마다 등록된 객체들(옵저버)에게 통지하는 패턴

```java
interface Observer {
    void sendExitMessage();
}

interface Subject {
    void registerObserver(Observer o);
    void removeObserver(Observer o);
    void notifyObservers();
}

class Cafe implements Subject {
    private List<Observer> observers = new ArrayList<>();

    public void registerObserver(Observer o) {
        observers.add(o);
    }

    public void removeObserver(Observer o) {
        observers.remove(o);
    }

    public void notifyObservers() {
        for (Observer observer : observers) {
            observer.sendExitMessage();
        }
    }

    public void closingTime() {
        notifyObservers();
    }
}

class Customer implements Observer {
    private String name;

    public Customer(String name) {
        this.name = name;
    }

    public void sendExitMessage() {
        System.out.println(name + " 퇴실 시간입니다.");
    }
}

public class Main {
    public static void main(String[] args) {
        Cafe cafe = new Cafe();
        for (int i = 0; i < 100; i++) {
            cafe.registerObserver(new Customer("Customer " + i));
        }

        cafe.closingTime();
    }
}
```

# Iterator 와 Observer 패턴의 차이점

## Iterator 패턴

컬렉션의 요소들을 순차적으로 접근하여 각 요소에 대해 어떤 작업을 수행할 때 사용 클라이언트에게 컬렉션의 구조를 숨기면서 요소에 안전하게 접근 가능하다.

### 적합한 시나리오

- 컬렉션 내의 모든 요소에 일관된 작업을 적용 해야할 때
- 다양한 자료구조에 대해 동일한 방법으로 요소에 접근하려고 할 때

### 적용 예시

**상황**: 카페 매니저가 카페의 모든 공간을 순회하면서 청소 상태를 확인

Iterator 패턴을 사용해 카페의 모든 공간을 순차적으로 접근하고 각 공간의 청소 상태를 확인한다. 만약 인테리어 변경 등으로 공간의 구조가 변경 되었다 하더라도 순회 로직에 영향을 주지 않기 때문에 모든 공간을 문제없이 확인할 수 있다.

## Observer 패턴

객체의 상태 변화를 관찰하고, 변화가 발생했을 때 하나 이상의 객체(옵저버)에게 자동으로 통지하도록 설계된 패턴, 비동기 프로그래밍에서 유리하며, 상태 변화에 대한 응답으로 여러 객체가 동시에 반응해야 할 때 적합하다.

### 적합한 시나리오

- 시스템의 한 부분(주제)의 상태 변경이 다른 여러 부분(옵저버)에 영향을 미쳐야 할 때
- 비동기적으로 여러 객체에게 이벤트를 통지해야 할 때

### 적용 예시

**상황**: 사장님이 갑자기 프로모션 이벤트를 열었다.

이벤트 정보를 업데이트하면 자동으로 모든 등록 손님들에게 알림이 가도록 옵저버 패턴을 사용한다. 이렇게 하면, 각 손님은 카페에서 발생한 이벤트에 대해 즉시 알 수 있다.
