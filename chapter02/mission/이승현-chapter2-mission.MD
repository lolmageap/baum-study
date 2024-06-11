# Chapter 2: encode, decode를 사용한 예시

## encode, decode

- 인코드(encode) : 데이터를 특정 형식이나 코드로 변환하는 과정
- 디코드(decode) : 데이터를 원래의 형식 또는 의미로 변환하는 과정

## AES 암호화 알고리즘

- 대칭 키 암호화 알고리즘으로 많이 사용 중
- 대칭 키 암호화 : 암호화와 복호화에 같은 키를 사용하는 방식
- 유연성 : 128, 192, 256 비트의 세 가지 키 길이를 지원, 보안 수준과 성능 요구 사항에 따라 선택 가능

## 예시

### 암호화 과정

```jsx
Cipher cipher = Cipher.getInstance("AES/ECB/PKCS5Padding");
            cipher.init(Cipher.ENCRYPT_MODE, SECRET);
            byte[] encryptedBytes = cipher.doFinal(plainText.getBytes(StandardCharsets.UTF_8));
```

1. **Cipher 인스턴스 생성**: **`Cipher.getInstance("AES/ECB/PKCS5Padding")`**는 **`Cipher`** 객체를 초기화합니다.
2. **암호화 모드 초기화**: **`cipher.init(Cipher.ENCRYPT_MODE, SECRET)`**는 Cipher 객체를 암호화 모드로 설정하며, 사용할 키(**`SECRET`**)를 지정합니다.
3. **암호화 실행**: **`cipher.doFinal(plainText.getBytes(StandardCharsets.UTF_8))`**는 주어진 평문(**`plainText`**)을 바이트로 변환하고 암호화를 수행합니다. 결과는 암호화된 데이터(**`encryptedBytes`**)입니다.

### 복호화 과정

```jsx
Cipher cipher = Cipher.getInstance("AES/ECB/PKCS5Padding");
cipher.init(Cipher.DECRYPT_MODE, SECRET);
byte[] decryptedBytes = cipher.doFinal(Hex.decodeHex(encryptedText));
```

1. **Cipher 인스턴스 생성**: 암호화와 동일하게 **`Cipher`** 객체를 "AES/ECB/PKCS5Padding" 설정으로 초기화합니다.
2. **복호화 모드 초기화**: **`cipher.init(Cipher.DECRYPT_MODE, SECRET)`**로 Cipher 객체를 복호화 모드로 설정합니다. 여기서도 같은 **`SECRET`** 키를 사용합니다.
3. **복호화 실행**: **`cipher.doFinal(Hex.decodeHex(encryptedText))`**는 암호화된 텍스트(**`encryptedText`**)를 Hexadecimal로부터 바이트 배열로 디코딩하고, 복호화를 수행합니다. 결과는 원래의 평문 데이터(**`decryptedBytes`**)입니다.
