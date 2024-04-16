## incode, decode를 사용한 예시

예시를 보여드리기 전에 인코딩 방식에는 크게 단방향 암호화와 양방향 암호화가 있습니다.

### 양방향 암호화 방식
양방향 암호화란 디코드가 가능한 암호화 방식을 말합니다.
저희 핀테크 팀에서 사용하는 라이브러리기도 한 jasypt을 사용하여 양방향 암호화의 예시를 작성해봤습니다.

jasypt 의존성을 받아주신 다음에 아래와 같이 사용하시면 됩니다.
```kotlin
implementation("com.github.ulisesbocchio:jasypt-spring-boot-starter")
```

암호화 키와 알고리즘을 설정해주고, application.yml에 암호화된 값을 넣어주시면 됩니다.
```kotlin
StandardPBEStringEncryptor().apply {
    setPassword(encryptKey)
    setAlgorithm(encryptAlgorithm)
}
```

```yaml
username: ENC(1/5Lr19WTv1Xoc90Tj5wyxSL9U5Y/Mcff6T8iMvrCkI=)
password: ENC(cBUbprMjnyXe2CkseVhLT2tSWy+HVEsN0UJchFaOheU=)
```

저희 팀만 알고 있는 encryptAlgorithm과 encryptKey를 사용하여 암호화된 값을 복호화하여 사용할 수 있습니다.

### 단반향 암호화 방식

단방향 암호화 방식은 복호화가 불가능한 암호화 방식을 말합니다.  
복호화를 할 수 없게 해놓은 이유는 대부분의 사용자들은 다른 서비스에서도 동일한 비밀번호를 사용 때문에 복호화가 가능하다면 사용자의 비밀번호가 노출될 수 있습니다.
하지만 복호화가 불가능한 단방향 암호화도 rainbow table을 사용하여 복호화가 가능할 수 있습니다.  

#### rainbow table
rainbow table은 해시 함수를 사용하여 암호화된 값을 저장해놓은 테이블입니다.
해시 함수는 동일한 입력값에 대해 항상 동일한 출력값을 반환하기 때문에 미리 계산해놓은 해시값을 비교하여 복호화를 할 수 있습니다.

해결 방안으로는 아래 예시인 salting을 사용하여 해결할 수 있습니다.
#### 회원 가입 시 예제
```kotlin
val encodedPassword = passwordEncoder.encode(request.password + request.salt)

userRepository.save(
    User.of(request.username, encodedPassword, request.salt)
)
``` 

#### 로그인 시 예제
```kotlin
val user = userRepository.findByUsername(request.username)
passwordEncoder.matches(request.password + user.salt, user.password)
```

![img.png](../image/정철희-image1.png)
