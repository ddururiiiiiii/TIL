
# **공유 락 (Shared Lock)과 베타 락 (Exclusive Lock)**

- 데이터베이스에서 동시성을 제어하기 위한 락의 두가지 유형.
- 여러 트랙잭션이 같은 데이터를 동시에 사용하려고 할 때, 데이터의 일관성과 무결성을 보장하기 위해 사용.

### 공유 락(Shared Lock)

- 공유 락은 데이터를 **읽기 전용**으로 사용하는 경우에 적용
- 여러 트랜잭션이 동시에 동일한 데이터에 공유 락을 걸 수 있음.
- 그러나, **쓰기 작업은 불가능**. 즉, 데이터를 수정하려는 트랜잭션은 대기해야 함.

### **예시**

1. **트랜잭션 A**: 고객 목록을 조회.
2. **트랜잭션 B**: 같은 고객 목록을 조회.
    - 둘 다 **공유 락**을 걸고 읽기 작업 수행 가능.
3. **트랜잭션 C**: 고객 목록에 새로운 고객 추가.
    - **쓰기 작업**이므로 대기해야 함.

### **베타 락(Exclusive Lock)**

- 베타 락은 데이터를 **읽기 및 쓰기 모두 제한**하는 강력한 락
- 한 트랜잭션이 베타 락을 걸면, **다른 트랜잭션은 읽기/쓰기 작업 모두 불가능**.
- 데이터 수정이 필요할 때 사용
- 강력한 동시성 제어를 제공하지만, 성능에 영향을 줄 수 있음.

### **예시**

1. **트랜잭션 A**: 특정 고객의 정보를 수정.
    - 베타 락을 걸고 작업 수행.
2. **트랜잭션 B**: 같은 고객의 정보를 조회.
    - 대기해야 함(읽기 불가능).
3. **트랜잭션 C**: 같은 고객의 정보를 삭제.
    - 대기해야 함(쓰기 불가능).

### **공유 락과 베타 락의 비교**

| **특징** | **공유 락 (Shared Lock)** | **베타 락 (Exclusive Lock)** |
| --- | --- | --- |
| **읽기 작업** | 여러 트랜잭션이 동시에 읽기 가능 | 읽기 쓰기 모두 불가능 |
| **쓰기 작업** | 허용하지 않음 | 한 트랜잭션만 쓰기 가능 |
| **다른 락 허용 여부** | 다른 공유 락 허용 | 다른 락 허용하지 않음 |
| **사용 예** | 데이터 조회 | 데이터 수정 또는 삭제 |

