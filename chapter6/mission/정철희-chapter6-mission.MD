# flyweight pattern

## flyweight pattern 이란?
flyweight pattern은 객체생성할 때 캐싱된 객체를 사용하여 메모리를 절약하는 패턴입니다.  

## flyweight pattern의 장점
객체를 재사용하여 메모리를 절약할 수 있습니다.  
그로 인해 객체 생성 시간이 줄어들어 성능이 향상됩니다.  
캐싱된 값을 사용하기 때문에 잘 변하지 않고 가벼운 객체를 사용할 때 적합합니다.  

## flyweight pattern의 단점
예시로 가장 많이 사용 되는 Int 타입은 decompile 되었을 때 java의 int 타입으로 변경이 됩니다.  
int 타입은 값 타입이기 때문에 flyweight pattern을 사용할 수 없습니다.  

하지만 Int? 타입은 다릅니다.  
Int? 타입은 java code로 decompile 되었을 때 Integer 타입으로 변경이 됩니다.  
Integer 타입은 wrapper class기 때문에 flyweight pattern이 적용될 수 있습니다.  
그래서 -128 ~ 127 사이의 값은 flyweight pattern을 적용됩니다.  
이런 이유를 모르고 사용한다면 원인 불명의 버그가 발생할 수 있고 파악하기도 어렵습니다.  
  
> flyweight pattern이 적용된 예시
 
![스크린샷 2024-05-15 오후 9.05.13.png](..%2F..%2F..%2F..%2FDesktop%2F%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202024-05-15%20%EC%98%A4%ED%9B%84%209.05.13.png)

> flyweight pattern이 적용되지 않은 예시
> 
![스크린샷 2024-05-15 오후 9.04.39.png](..%2F..%2F..%2F..%2FDesktop%2F%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202024-05-15%20%EC%98%A4%ED%9B%84%209.04.39.png)
