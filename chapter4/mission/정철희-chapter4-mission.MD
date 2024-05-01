## iterator pattern

```kotlin
interface Iterator {
    fun isUpdated(): Boolean
}

class Station(
    val stations: ArrayList<NewsLetter>,
) : Iterator {
    override fun isUpdated(): Boolean {
        val isUpdate = stations.filter { it.isUpdated }
        return isUpdate.isNotEmpty()
    }
}

class NewsLetter {
    var newestArticleUrl: String = ""
        set(value) {
            field = value
            isUpdated = true
        }

    var isUpdated: Boolean = false
}
```

iterator pattern은 상태를 가진 객체를 순차적으로 접근하는 디자인 패턴입니다.  
위 코드를 보면 알 수 있듯이 Station 클래스는 NewsLetter 클래스의 상태를 순차적으로 접근하여 isUpdated가 true인지 확인합니다.  
cpu의 외부 interrupt가 존재하지 않을 때는 이처럼 주기적으로 isUpdated 함수를 통해 외부 장치의 상태를 확인해야 합니다.  

## observer pattern

```kotlin
interface IObserver {
    fun update()
}

interface IObservable {
    val observers: ArrayList<IObserver>

    fun add(observer: IObserver) {
        observers.add(observer)
    }

    fun remove(observer: IObserver) {
        observers.remove(observer)
    }

    fun sendUpdateEvent() {
        observers.forEach { it.update() }
    }
}

class Newsletter: IObservable {
    override val observers: ArrayList<IObserver> = ArrayList()
    var newestArticleUrl = ""
        set(value) {
            field = value
            sendUpdateEvent()
        }
}
```

observer pattern은 객체의 상태 변화를 관찰하는 객체를 만들어, 상태 변화가 있을 때마다 이를 통지받는 디자인 패턴입니다.  
위 코드를 보면 알 수 있듯이 Newsletter 클래스는 newestArticleUrl이 변경 될 때마다 IObserver가 호출 되어 상태 변화의 변경을 알 수 있습니다.  
cpu의 외부 interrupt가 존재할 때는 외부 장치가 interrupt를 발생시켜 상태 변화가 있을 때마다 이를 통지받게 됩니다.  
## iterator pattern

```kotlin
interface Iterator {
    fun isUpdated(): Boolean
}

class Station(
    val stations: ArrayList<NewsLetter>,
) : Iterator {
    override fun isUpdated(): Boolean {
        val isUpdate = stations.filter { it.isUpdated }
        return isUpdate.isNotEmpty()
    }
}

class NewsLetter {
    var newestArticleUrl: String = ""
        set(value) {
            field = value
            isUpdated = true
        }

    var isUpdated: Boolean = false
}
```

iterator pattern은 상태를 가진 객체를 순차적으로 접근하는 디자인 패턴입니다.  
위 코드를 보면 알 수 있듯이 Station 클래스는 NewsLetter 클래스의 상태를 순차적으로 접근하여 isUpdated가 true인지 확인합니다.  
cpu의 외부 interrupt가 존재하지 않을 때는 이처럼 주기적으로 isUpdated 함수를 통해 외부 장치의 상태를 확인해야 합니다.  

## observer pattern

```kotlin
interface IObserver {
    fun update()
}

interface IObservable {
    val observers: ArrayList<IObserver>

    fun add(observer: IObserver) {
        observers.add(observer)
    }

    fun remove(observer: IObserver) {
        observers.remove(observer)
    }

    fun sendUpdateEvent() {
        observers.forEach { it.update() }
    }
}

class Newsletter: IObservable {
    override val observers: ArrayList<IObserver> = ArrayList()
    var newestArticleUrl = ""
        set(value) {
            field = value
            sendUpdateEvent()
        }
}
```

observer pattern은 객체의 상태 변화를 관찰하는 객체를 만들어, 상태 변화가 있을 때마다 이를 통지받는 디자인 패턴입니다.  
위 코드를 보면 알 수 있듯이 Newsletter 클래스는 newestArticleUrl이 변경 될 때마다 IObserver가 호출 되어 상태 변화의 변경을 알 수 있습니다.  
cpu의 외부 interrupt가 존재할 때는 외부 장치가 interrupt를 발생시켜 상태 변화가 있을 때마다 이를 통지받게 됩니다.  