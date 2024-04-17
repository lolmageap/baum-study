# incode, decode를 사용한 예시

인코딩에는 양방향 암호화와 단방향 암호화가 있습니다.  
예제와 함께 간단하게 암호화의 방식에 대해 알아보겠습니다.
<br/>
<br/>
<br/>
## 1. 양방향 암호화
양방향 암호화 방식이란 데이터를 암호화하고 암호화된 데이터에 대한 복호화가 가능한 방식입니다.
<br/>
<br/>
대표적으로 대칭키 암호화 방식과 비대칭키 암호화 방식이 있습니다.
<br/>
<br/>
#### * 대칭키 암호화
암호화와 복호화에 같은 키를 사용합니다.  
처리 속도가 빠르고 효율적이기 때문에 대량의 데이터를 암호화해야 할 때 자주 사용됩니다.  
대표적인 방식에는 AES, DES, 3DES가 있습니다.
<br/>
<br/>
#### * 비대칭키 암호화
암호화에는 공개키를 사용하고 복호화에는 비밀키(개인키)를 사용합니다.  
대칭키에 비해 처리 속도가 느리지만, 키 관리의 안정성이 높아 외부로 데이터를 전송할 때 주로 사용됩니다.  
대표적인 방식에는 RSA, ECC, Diffie-Hellman이 있습니다.
<br/>
<br/>
이 중 대칭키 방식인 AES를 사용하여 인코딩과 디코딩을 해보았습니다.
```kotlin
val key = ""
val cipher = Cipher.getInstance("AES/CBC/PKCS5Padding")
```
key의 길이에 따라 AES-128, AES-192, AES-256으로 구분되므로 원하는 암호화 방식에 맞게 작성해 줍니다.  
또한 Cipher 인스턴스를 생성하여 AES, CBC 모드, PKCS5 패딩을 사용하도록 합니다.
<br/>
<br/>
CBC 모드는 같은 평문 블록에 대해서도 다른 암호문 블록을 생성하여 보안을 강화합니다.  
또 AES 암호화는 고정된 크기의 데이터 블록을 처리하는데, 이때 평문 데이터의 길이가 블록 크기와 일치하지 않을 경우 패딩으로 데이터 블록을 채워 고정된 길이를 유지해야 하므로 PKCS5를 사용합니다.
<br/>
<br/>
```kotlin
cipher.init(
Cipher.ENCRYPT_MODE,
SecretKeySpec(key.toByteArray(), "AES"),
IvParameterSpec(key.substring(0, 16).toByteArray())
)
return Base64.getEncoder().encodeToString(cipher.doFinal(str.toByteArray(StandardCharsets.UTF_8)))
```
cipher 객체를 암호화 모드, 사용할 키 그리고 초기화 벡터를 설정하여 암호화 준비를 합니다.  
이후 인코딩하고 암호화된 데이터를 전송하거나 저장하기 위해 문자열로 반환합니다.
<br/>
<br/>
```kotlin
cipher.init(
Cipher.DECRYPT_MODE,
SecretKeySpec(key.toByteArray(), "AES"),
IvParameterSpec(key.substring(0, 16).toByteArray())
)
return String(cipher.doFinal(Base64.getDecoder().decode(encStr.toByteArray(StandardCharsets.UTF_8))))
```
마찬가지로 cipher 객체를 복호화 모드로 설정하여 복호화 한 후 문자열로 반환합니다.
<br/>
<br/>
<br/>
## 2. 단방향 암호화
단방향 암호화 방식이란 인코딩한 데이터를 복호화 할 수 없는 방식입니다.
<br/>
<br/>
단방향 암호화의 핵심은 동일한 입력값에 대해 항상 동일한 암호문을 생성하지만, 암호문에서는 원래의 입력값을 알아낼 수 없다는 점입니다.
<br/>
<br/>
주로 비밀번호 저장이나 데이터 무결성 검증 등에 사용되며, 대표적인 방법으로는 해시 함수를 사용하여 암호화하는 방식이 있습니다.
<br/>
<br/>
#### * 해시 함수
해시 함수는 임의의 길이 데이터를 입력받아 고정된 길이의 해시값을 출력하는 함수입니다.  
SHA-256이 대표적이며, 이는 256 비트 길이의 해시값을 생성합니다.
<br/>
<br/>
앞서 단방향 암호화는 동일한 입력값에 대해 항상 동일한 암호문을 생성한다 했는데, 이러한 특징이 또 다른 문제가 되기도 합니다.
<br/>
<br/>
#### * Rainbow Table Attack
동일한 입력값에 대해 항상 동일한 암호문을 생성하는 특성을 이용해 모든 암호에 대해 테이블화해두었다면, 결과를 보고 원래의 암호를 알아낼 수 있게 됩니다. 이렇게 데이터베이스화 해둔 테이블을 레인보우 테이블이라 합니다.
<br/>
<br/>
이를 보완하기 위해 salt나 key stretching 을 사용하는 방법이 있습니다.
<br/>
<br/>
#### * salt
솔트는 해시 과정에서 데이터에 무작위로 추가되는 데이터 조각입니다.  
같은 데이터에 고유한 솔트를 추가한다면, 동일한 데이터라도 다른 해시값이 생성됩니다.
<br/>
<br/>
#### * key stretching
키 스트레칭 기법은 해시 과정을 여러 번 반복하여 계산 시간을 인위적으로 늘리는 방법입니다.  
이는 공격자가 레인보우 테이블을 사용하거나 무차별 대입 공격을 시도할 때 필요한 시간을 크게 증가시킵니다.
<br/>
<br/>
<br/>
아래는 SHA-256에 솔트와 키 스트레칭 기법을 사용하여 암호화하였습니다.
```kotlin
val random = SecureRandom()
val salt = ByteArray(16)
random.nextBytes(salt)
```
먼저 솔트를 생성합니다.
<br/>
<br/>
```kotlin
val spec = PBEKeySpec(str.toCharArray(), salt, 10, 256)
val factory = SecretKeyFactory.getInstance("PBKDF2WithHmacSHA256")
val hash = factory.generateSecret(spec).encoded
<br/>
<br/>
return hash.joinToString("") { "%02x".format(it) }
```
솔트와 반복 횟수 그리고 해시 길이를 지정하고, PBKDF2 알고리즘으로 해싱 하여 보다 안전하게 암호화를 하였습니다.
<br/>
