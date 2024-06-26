## **고급 프로그래밍 언어**

고급 프로그래밍 언어는 개발자가 컴퓨터에 명령을 내리기 위해 사용하는 언어로, 이 언어들은 복잡한 알고리즘과 데이터 구조를 쉽게 표현할 수 있고, 추상화된 명령어와 문법을 통해 개발자가 코드를 더 쉽게 이해하고 작성할 수 있도록 돕습니다. 예를 들어, **`if`** 조건문, **`for`** 또는 **`while`** 반복문은 개발자가 제어 흐름을 직관적으로 표현할 수 있게 해 줍니다. 작성된 코드는 컴파일러나 인터프리터를 통해 컴퓨터가 이해할 수 있는 기계어로 변환되어 실행됩니다.

## **저급 언어**

저급 언어는 컴퓨터 하드웨어와 직접적으로 상호 작용하는 언어로, 기계어와 어셈블리어가 있습니다.

### **기계어**

기계어는 컴퓨터의 CPU가 직접 이해하고 수행할 수 있는 언어로, 0과 1의 명령어로 이루어진 명령어 모음입니다. 이러한 언어는 인간이 이해하기 매우 어려우며, 주로 하드웨어 수준에서의 프로그래밍에 사용됩니다.

### **어셈블리어**

어셈블리어는 기계어를 조금 더 인간이 이해할 수 있는 형태로 표현한 언어입니다. 각 어셈블리어 명령어는 기계어 명령어와 일대일로 대응되며, **`MOV`**, **`ADD`**, **`SUB`**와 같은 명령어를 통해 CPU에 직접적인 명령을 내릴 수 있습니다. 이 언어는 하드웨어에 가까운 수준에서 세밀한 제어가 필요한 경우에 사용되며, 특히 임베디드 시스템, 게임 개발, 정보 보안 등에서 중요합니다.

```nasm
; 예시: LED를 켜고 끄는 ARM 어셈블리 코드
MOV R0, #1      ; R0 레지스터에 1을 로드 (LED ON)
STR R0, [GPIO]  ; GPIO 주소에 R0의 값을 저장하여 LED를 켬
MOV R0, #0      ; R0 레지스터에 0을 로드 (LED OFF)
STR R0, [GPIO]  ; GPIO 주소에 R0의 값을 저장하여 LED를 끔
```

## **컴파일러와 인터프리터**

### **컴파일러**

컴파일러는 소스 코드 전체를 분석하여 기계어로 변환한 후, 실행 가능한 파일 형태로 저장하는 소프트웨어입니다. 컴파일 과정에서 코드 최적화가 이루어지기 때문에 실행 속도가 빠르다는 장점이 있습니다. 컴파일러는 오류를 발견하기 쉽고, 오류가 발견되면 전체 컴파일 과정을 중단하므로, 오류 수정이 필수적입니다. 예로 C, C++, Java 언어가 컴파일러를 사용합니다.

### **인터프리터**

인터프리터는 소스 코드를 한 줄씩 읽고 해석하여 즉시 실행하는 방식을 사용합니다. 이는 실시간 코드 수정과 테스트를 가능하게 하여, 개발 및 디버깅 과정을 용이하게 합니다. 그러나 이 방식은 전체 코드를 한 번에 컴파일하는 것보다 실행 속도가 느릴 수 있습니다. JavaScript, Python, Ruby와 같은 스크립트 언어들이 주로 인터프리터를 사용합니다.

## **명령어의 구조**

명령어는 크게 연산코드와 오퍼랜드로 구성됩니다.

### **연산코드**

연산코드는 프로세서가 수행해야 할 정확한 연산을 명시합니다. 

1. **데이터 전송 연산**: 데이터를 메모리 위치에서 다른 메모리 위치로, 또는 레지스터 간에 이동시키는 작업을 지시합니다.
    1. MOVE : 데이터 이동
    2. STORE: 메모리에 저장
    3. LOAD(FETCH): 메모리에서 CPU로 데이터를 로드
    4. PUSH: 스택에 데이터 저장
    5. POP: 스택 최상단 데이터 로드
2. **산술 연산**: 수학적 계산을 수행하는 연산입니다.
    1. ADD/ SUB/ MUL/ DIV
    2. INCREMENT/ DECREMENT
3. **논리 연산**: 비트 단위의 논리적 연산을 수행합니다.
    1. AND/ OR/ NAND/ XOR/ NOT
    2. COMPARE
4. **제어 흐름 연산**: 프로그램의 실행 흐름을 변경하는 명령어입니다.
    1. JUMP: 특정 주소로 실행 순서 이동
    2. CONDITIONALJUMP: 조건에 부합하면 특정 주소로 이동
    3. HALT: 프로그램 실행 정지
    4. CALL: 되돌아올 주소를 저장한 채 특정 주로 실행 순서를 옮김
    5. RETURN: CALL을 호출한 주소로 돌아가라
5. **입출력 제어 연산**: 입출력 장치를 제어하기 위한 명령어입니다.
    1. READ/ WRITE/ START IO/ TEST IO

### **오퍼랜드**

오퍼랜드는 연산에 사용할 데이터나, 데이터의 위치를 지정하는 부분입니다. 많은 경우 연산에 사용할 데이터가 저장된 위치, 즉 메모리 주소나 레지스터 이름이 담깁니다. 그래서 주소 필드라고 부르기도 합니다.

### **주소 지정 방식**

주소 지정 방식은 오퍼랜드 필드에 데이터가 저장된 위치를 명시할 때 연산에 사용할 데이터 위치를 찾는 방법입니다.

1. **즉시 주소 지정 방식**: 명령어 자체가 실제로 사용될 데이터 값을 직접 포함합니다. 이 방식은 데이터 처리를 빠르게 하지만, 데이터의 크기가 작아지는 단점이 있습니다.
2. **직접 주소 지정 방식**: 명령어가 데이터의 정확한 메모리 주소를 포함하고 있습니다. 여전히 유효 주소를 표현할 수 있는 범위가 연산 코드의 비트 수만큼 줄어들었습니다.
3. **간접 주소 지정 방식**: 메모리 주소가 레지스터나 다른 메모리 위치에 의해 간접적으로 참조됩니다. 즉 유효 주소의 주소를 오퍼랜드에 명시합니다.
4. **레지스터 주소 지정 방식**: 오퍼랜드가 레지스터를 참조하며, CPU 내부에 있는 레지스터에 접근 하는 것 이기 떄문에 매우 빠른 데이터 접근을 가능하게 합니다. 대신 표현할 수 있는 레지스터 크기에 제한이 생길 수 있습니다.
5. 레지스터 간접 주소 지정 방식: 간접 주소 방식과 같으나 레지스터에 메모리의 유효 주소를 저장해 메모리에 접근하는 횟수가 한 번으로 줄어든다는 장점이 있습니다.

## **스택과 큐**

### **스택**

스택은 후입선출(LIFO) 방식의 데이터 구조로, 주로 프로그램에서 함수의 호출과 반환에서 사용됩니다. 예를 들어, 함수가 다른 함수를 호출할 때, 호출한 함수의 실행 상태를 스택에 저장하고, 함수가 반환될 때 이를 다시 꺼내 이전 상태로 복귀합니다.

### **큐**

큐는 선입선출(FIFO) 방식의 데이터 구조로, 데이터의 처리 순서를 유지해야 할 때 사용됩니다. 예를 들어, 프린터의 인쇄 작업 관리, 운영 체제에서의 프로세스 스케줄링, 네트워크 트래픽 관리 등에 큐가 사용됩니다. 이러한 애플리케이션에서는 데이터나 작업이 도착한 순서대로 처리되어야 하기 때문에 큐가 이상적입니다.
