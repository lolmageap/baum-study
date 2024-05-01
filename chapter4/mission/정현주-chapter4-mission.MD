## Iterator 패턴
Iterator 패턴은 컬렉션의 구현 방식을 노출시키지 않으면서 컬렉션의 요소들을 순차적으로 접근할 수 있게 해주는 디자인 패턴입니다.
</br>
</br>
Iterator 패턴을 사용하여 장치 리스트를 순회하면서 각 장치의 상태를 체크합니다.
```kotiln
class Device(val name: String) {
    val status = "OK"

    fun checkStatus(): String {
        return "Status of $name: $status"
    }
}
```
Device 클래스를 정의하고 각 장치는 이름과 상태를 가리키며, 장치의 현재 상태를 반환하는 메서드를 정의합니다.
</br>
</br>
```kotiln
class DeviceStatusIterator(
  val devices: List<Device>,
): Iterator<String> {
    private var index = 0

    override fun hasNext(): Boolean {
        return index < devices.size
    }

    override fun next(): String {
        if (!hasNext()) {
            throw NoSuchElementException()
        }
        return devices[index++].checkStatus()
    }
}
```
장치 리스트를 순회하기 위한 Iterator 클래스를 정의합니다.  
리스트에 더 처리할 장치가 있는지 확인하는 메서드와 다음 장치의 상태를 반환하고 인덱스를 하나 증가시키는 메서드를 정의합니다.
</br>
</br>
```kotiln
fun main() {
    val devices = listOf(Device("Sensor"), Device("Camera"), Device("Printer"))
    val iterator = DeviceStatusIterator(devices)

    while (iterator.hasNext()) {
        println(iterator.next())
    }
}
```
메인 함수에서는 장치 리스트를 생성하고, 이를 Iterator로 순회하면서 각 장치의 상태를 출력합니다.
</br>
</br>
</br>

## Observer 패턴
Observer 패턴은 객체 간의 일대다 의존 관계에서 한 객체의 상태 변화가 발생하면, 그 객체에 의존하는 모든 객체들이 자동으로 알림을 받고 갱신되는 구조를 가지는 패턴입니다.  
즉, 객체의 상태 변화를 관찰하고 변화가 있을 때 등록된 객체들에게 자동으로 알림을 보내는 디자인 패턴입니다.
</br>
</br>
Observer 패턴을 사용하여, 각 장치의 상태 변경을 감지하고 해당 변경을 처리합니다.
```kotiln
class DeviceObservable(val name: String): Observable() {
    val status = "OK"
        set(value) {
            field = value
            setChanged()
            notifyObservers(value)
        }
}
```
DeviceObservable 클래스는 Observable을 상속받아 장치의 상태 변화를 관찰할 수 있게 합니다.
또한 status 필드의 값을 업데이트하고, Observable 클래스의 상태가 변경되었음을 표시하고, 모든 Observer에게 변경 사항을 통지합니다.
</br>
</br>
```kotiln
class StatusObserver: Observer {
    override fun update(
      o: Observable?,
      arg: Any?,
    ) {
        if (o is DeviceObservable) {
            println("Status of ${o.name} changed to $arg")
        }
    }
}
```
StatusObserver 클래스는 Observer 인터페이스를 구현하여 상태 변화를 받아 처리합니다.
Observable이 DeviceObservable 인스턴스인지 확인하고, 상태 변화를 출력하는 메서드를 정의합니다.
</br>
</br>
```kotiln
fun main() {
    val observer = StatusObserver()
    val device1 = DeviceObservable("Sensor")
    val device2 = DeviceObservable("Camera")
    val device3 = DeviceObservable("Printer")

    device1.addObserver(observer)
    device2.addObserver(observer)
    device3.addObserver(observer)

    device1.status = "Error"
    device2.status = "Offline"
    device3.status = "Maintenance"
}
```
메인 함수에서는 여러 DeviceObservable 객체를 생성하고 StatusObserver를 등록합니다.
Observer 인스턴스 생성하고, 각 장치에 Observer를 추가하고, 상태 변화를 시뮬레이션합니다.
