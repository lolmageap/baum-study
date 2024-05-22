`````

💡 데이터베이스의 성능을 최적화하고 디스크 I/O의 느린 속도를 극복하기 위해 여러 가지 전략과 기술을 사용할 수 있습니다. 아래는 몇 가지 주요 방법들

`````

# 1. **캐싱 (Caching)**

- 자주 접근하는 데이터나 계산 결과를 임시로 저장해 두고, 필요할 때마다 빠르게 접근할 수 있도록 하는 방법
- 장점 : 메모리에서 읽기 때문에 속도가 매우 빠름
- 단점 : 데이터베이스의 데이터와 불일치 할 가능성 존재

# 2. **파티셔닝 (Partitioning)**

- 큰 테이블을 더 작은 부분(파티션)으로 나누어 관리하는 방법
- 장점 : 특정 파티션에만 접근하기 때문에 검색 속도 높아짐, 큰 테이블의 관리가 용이해짐
- 단점 : 파티셔닝 설정 및 관리 복잡, 여러 파티션을 탐색해야하는 경우 오히려 성능 저하 유발

```sql
CREATE TABLE sales (
    sale_id INT PRIMARY KEY,
    sale_date DATE,
    amount DECIMAL(10, 2)
) PARTITION BY RANGE (YEAR(sale_date));
-------------------------------------------
CREATE TABLE sales_2022 PARTITION OF sales
FOR VALUES FROM ('2022-01-01') TO ('2022-12-31');

CREATE TABLE sales_2023 PARTITION OF sales
FOR VALUES FROM ('2023-01-01') TO ('2023-12-31');
```

# 3. **인덱싱 (Indexing)**

- 컬럼의 값을 기반으로 인덱스(데이터 구조)를 만드는 방법
- 장점 : 인덱스를 사용하여 빠른 데이터 검색 가능
- 단점 : 인덱스를 유지하기 위해 추가적인 저장 공간이 필요, 데이터 삽입, 삭제, 업데이트 시 인덱스도 함께 갱신해야 하므로 성능 저하 유발

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    name VARCHAR(100),
    department VARCHAR(100),
    salary DECIMAL(10, 2)
);
-------------------------------------
CREATE INDEX idx_department ON employees(department);
```

# 4. **쿼리 최적화 (Query Optimization)**

- 쿼리를 재구성하여 효율적으로 처리함하여 속도를 높이는 방법
- 장점 : 쿼리 실행 시간이 줄어듦, 리소스를 보다 더 효율적으로 사용할 수 있음
- 단점 : 쿼리의 복잡도가 높아짐, 유지보수 어려움