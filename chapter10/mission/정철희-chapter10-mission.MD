# Mission 10 

데이터 영역은 상수, 전역 변수, static 변수 등이 저장되는 영역입니다.  
Java는 데이터 영역(static area)이 왜 heap 영역에 포함되어 있을까요?
그 이유는 데이터 영역이 heap 메모리에 저장됨으로써 메모리 구조의 일관성을 유지할 수 있기 때문입니다. 

## PermGen 영역이 Metaspace로 변경된 이유

![img.png](../image/정철희-image6.png)

바뀐 이유를 살펴보자면 Java 7에서는 PermGen 영역이 어플리케이션 실행 될 때 고정 크기로 설정되어 있어서 클래스 메타데이터가 많아질 경우 쉽게 `OutOfMemoryError`가 발생했습니다.  
여기서 `클래스 메타데이터`란 클래스 로더가 런타임 중에 읽어온 정보입니다.  
이 문제 해결하기 위해서 Java 8에서는 PermGen 영역을 제거하고 Metaspace를 도입하여 클래스 메타데이터를 네이티브 메모리에 저장합니다.  

- Heap 영역은 JVM에 의해 관리되는 영역이고 Native Memory는 OS에 의해 관리되는 영역입니다.  

Native Memory 영역에 있는 Metaspace는 OS가 자동으로 크기를 조절해주기 때문에 메모리 관리가 더욱 유연해집니다.  
