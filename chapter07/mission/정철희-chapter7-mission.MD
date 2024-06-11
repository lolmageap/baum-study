어떤 논리함수도, NAND GATE 만으로 또는 NOR GATE 만으로 구현이 가능합니다.

# NAND GATE

![img.png](../image/정철희-image11.png)

NAND GATE는 AND GATE의 출력을 NOT GATE에 연결한 것과 같습니다.

입력이 하나라도 0이면 1을 출력합니다.
입력이 모두 1인 경우에만 0을 출력합니다.

여기서 1은 true, 0은 false를 의미합니다.

NAND GATE는 일반적으로 OR GATE보다 빠르고 적은 부품을 사용합니다.

NAND GATE를 코드로 작성하면 아래처럼 작성할 수 있습니다.
```kotlin
fun nandGate(
    a: Boolean,
    b: Boolean,
) = !(a && b)
```

# NOR GATE

![img_1.png](../image/정철희-image12.png)

NOR GATE는 OR GATE의 출력을 NOT GATE에 연결한 것과 같습니다.

입력이 하나라도 1이면 0을 출력합니다.
입력이 모두 0인 경우에만 1을 출력합니다.

NOR GATE는 일반적으로 AND Gate보다 더 많은 부품을 사용합니다.
그런데 왜 NOR GATE를 사용할까요?

NOR GATE를 사용하게 되면 특정 상황에서 더 간단하게 표현할 수 있습니다. 

예를 들면 리셋 우선 비동기식 플립플롭 회로와 같은 특정 메모리 소자 설계에서는 NOR GATE가 더 적합할 수 있습니다.

여기서 비동기식 플립플롭 회로란 데이터를 저장하는 회로로 데이터를 저장하고 읽어 오는 동작을 수행합니다.

NOR GATE를 코드로 작성하면 아래처럼 작성할 수 있습니다.
```kotlin
fun norGate(
    a: Boolean,
    b: Boolean,
) = !(a || b)
```

