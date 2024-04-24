```
✅ 함수의 호출이 많아지면 오버헤드가 발생할 수 있음에도 불구하고 함수를 사용하는 이유
```

- 오버헤드 : 특정 작업을 수행하기 위해 필요한 추가적인 처리 시간 또는 메모리같은 자원을 의미 → 성능저하 유발 가능성
- 이유 : 장점 > 단점

---

## 함수 장점

1. 코드 재사용성

→ 함수는 특정 작업 수행 코드의 집합으로 필요할 때마다 여러 번 재사용할 수 있음

1. 가독성 및 유지보수

→ 함수 작업과 관련된 것으로 이름을 작성하면, 해당 함수의 역할이 무엇인지 쉽게 파악이 가능함

→ 이와 같은 장점은 코드 이해시간 단축과 오류를 빠르게 찾고 수정할 수 있게 함

---

## 함수 예시코드

- 두 수의 합을 계산하는 함수

```jsx
public class Main {

    public static int addNumbers(int a, int b) {
        return a + b;
    }

    public static void main(String[] args) {
        int result = addNumbers(1, 2);
        System.out.println(result);
    }
}
```

---

## 오버헤드 줄이는 방법

- 인라인화(**inline**) : 함수 호출 대신 호출 위치에 함수 내용을 직접 작성하는 컴파일러 최적화 기법
- 자바에서 인라인화는 컴파일러에 의해 자동으로 수행
- 컴파일러는 복잡성, 크기, 호출 빈도 등을 고려하여 인라인화를 결정

```jsx
public class Main {

    public static void main(String[] args) {
        int a = 1;
        int b = 2;
        int result = a + b;  // 함수 본문을 여기에 직접 삽입합니다.
        System.out.println(result);  // 결과 출력
    }
}
```
