## 단일 쿼리로 접근

- 장점
    - 네트워크 트래픽이 다중 쿼리보다 적어 데이터베이스와 서버와의 통신 시간을 최소화 한다.
    - 트랜잭션의 복잡성이 줄고 데이터의 일관성이 보장된다.
- 단점
    - 서버에 과도한 부하를 주어 시스템 성능에 영향을 줄 수 있다.
    - 수정이 어렵고 쿼리를 파악하는데 시간이 소요 될 수 있다.
- 적절한 상황
    - 통계와 같이 복잡한 분석을 수행할 때 유용하다.
    - 네트워크 트래픽을 최소화 하는 상황에서 유용하다.

## 다중 쿼리로 접근

- 장점
    - 쿼리를 나누어 필요한 데이터만 선택적으로 조회 조합 할 수 있어 유연성이 늘어난다.
    - 작은 단위의 쿼리이기 때문에 오류, 유지보수가 쉽다
- 단점
    - 데이터베이스와 빈번한 연결로 인해 성능 저하가 발생할 수 있다.
    - 작은 단위로 모두 트랜잭션 관리가 필요할 수 있어 관리가 힘들 수 있다.
- 적절한 상황
    - 자주 변경되는 환경에서 쿼리를 쉽게 수정하고 유지보수 할 수 있다.
    - 사용자의 요청에 따라 필요한 데이터만 동적으로 처리하는 상황에서 유리할 수 있다.
