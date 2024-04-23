## 함수를 사용 하는 이유

- 코드의 가독성을 높일 수 있다.
- 코드의 중복을 제거할 수 있다.

## Kotlin 에서 함수 사용하기
Kotlin은 고차함수와 같은 함수형 프로그래밍을 지원해주기 때문에 함수를 사용하는데 있어서 매우 편리합니다.
여기서 고차함수란 함수를 인자로 받거나 함수를 반환하는 함수를 말합니다.

```kotlin
fun main() {
    val first = "첫번째"
    val second = "두번째"
    match(first, second) { "두 값이 일치하지 않습니다." }
}

fun <T> match(first: T, second: T, message: () -> String) {
    if (first != second) {
        val errorMessage = message.invoke()
        throw IllegalArgumentException(errorMessage)
    }
}
```

확장 함수와 아래처럼 응용하게 된다면 보다 더 객체지향적으로 코드를 사용하고 가독성을 높일 수 있습니다.
```kotlin
@Transactional
fun login(
    email: String,
    password: String,
) {
    val user = userRepository.findByEmail(email)

    passwordEncoder.ifNotMatched(password, user.password) {
        userRepository.increaseLoginFailCount(user)
    }
}

fun PasswordEncoder.ifNotMatched(
    password: String,
    encodedPassword: String,
    block: () -> Unit,
) {
    if (!matches(password, encodedPassword)) {
        block.invoke()
        throw InvalidPasswordException()
    }
}
```

## 함수 호출 시 오버헤드를 줄일 수 있는 방법
kotlin에서는 inline 키워드를 사용하여 함수 호출 시 오버헤드를 줄일 수 있습니다.  
inline 키워드는 컴파일 타임에 함수를 호출하는 곳에 함수의 내용을 복사하여 함수를 호출하는 것이 아닌 함수의 내용을 그대로 복사하여 함수를 호출하는 방식입니다.  

```kotlin
inline fun PasswordEncoder.ifNotMatched(
    password: String,
    encodedPassword: String,
    block: () -> Unit,
) {
    if (!matches(password, encodedPassword)) {
        block.invoke()
        throw InvalidPasswordException()
    }
}
```