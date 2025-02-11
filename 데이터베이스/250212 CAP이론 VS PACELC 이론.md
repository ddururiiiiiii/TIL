
# **CAP 이론**
- **분산 시스템**에서 데이터 일관성과 가용성을 유지하려고 할 때, 네트워크 분할/장애(Partition Tolerance) 상황에서 일관성, 가용성, 분할허용성 이 **세 가지 속성 중 두 가지만 보장**할 수 있다는 이론
- 분산 시스템에서 네트워크 장애가 언제든 발생할 수 있고, 네트워크가 끊어지더라도 시스템의 일부가 계속 동작해야 하기 때문에 분할 허용성은 무조건 항상 유지되어야 하고, 즉 일관성, 가용성은 서로 상충하는 관계 이기 때문에 동시에 충족 할 수 없고 둘 중에 하나만 보장할 수 있다는 말.

| **속성** | **설명** |
| --- | --- |
| **Consistency (일관성)** | 모든 클라이언트가 항상 동일한 데이터를 볼 수 있어야 함.
a서버에서 데이터가 저장되었다면, b서버에서도 즉시 동일한 데이터를 조회할 수 있어야 함. |
| **Availability (가용성)** | 모든 요청에 대해 항상 응답을 제공해야 함. 일부 장에를 겪더라도 서비스가 계속 동작해야 함. |
| **Partition Tolerance (분할허용성)** | 네트워크 장애(분할) 상황에서도 시스템이 동작해야 함.
데이터센터 간 네트워크 문제가 발생하더라도, 각 데이터 센터는 독립적으로 작동 가능해야 함. |


### **CAP 이론을 따른 데이터베이스 설계**
### 1. **CP (Consistency + Partition Tolerance) /  일관성 우선**
- 네트워크 장애 시 **데이터 일관성**을 보장하지만, 가용성을 희생.
- 클라이언트 요청을 차단하거나 대기시켜 데이터의 정확성을 유지.
- **결과**: 데이터는 일관되지만, 일부 클라이언트 요청이 실패하거나 대기.
- **예시**: 전통적인 관계형 데이터베이스.
    - **MongoDB**: 기본 설정에서 일관성을 보장.
    - **HBase**: 쓰기 작업에 대해 일관성 보장.

### 2. **AP (Availability + Partition Tolerance)**
- 네트워크 장애 시 **서비스 가용성**을 보장하지만, 데이터 일관성을 희생.
- 각 노드가 독립적으로 동작하면서, 잠시 동안 데이터 불일치 가능.
- **결과**: 시스템은 동작하지만, 일시적으로 데이터가 서로 다른 값을 가질 수 있음.
- **예시**: 분산된 NoSQL 데이터베이스.
    - **Cassandra**: 데이터 읽기/쓰기에 초점을 맞춤.
    - **DynamoDB**: 높은 가용성을 보장.

# **PACELC 이론**
- CAP 이론의 확장으로, 분산 시스템에서 **네트워크 분할(Partition)**이 발생했을 때뿐만 아니라, **정상적인 상태**에서도 고려해야 할 속성을 제시하는 이론.
- CAP 이론: **Partition(네트워크 분할)** 상황에서 **Consistency(일관성)**와 **Availability(가용성)** 중 하나를 선택.
- PACELC 이론: Partition이 발생하지 않을 때도 **Latency(지연시간)**와 **Consistency(일관성)** 중 하나를 선택해야 한다고 주장.


### **PACELC 이론의 선택**
분산 시스템은 다음 네 가지 조합으로 나뉨.
1. **PA/EL (Partition: Availability, Else: Latency)**
    - Partition 발생 시 **가용성(Availability)**을 우선.
    - Partition이 없을 때 **지연시간(Latency)**을 줄이는 데 집중.
    - **예시**: **Cassandra**.
        - 네트워크 분할 상황에서도 서비스 제공을 우선시.
        - 정상 상태에서는 낮은 지연시간을 유지.
2. **PA/EC (Partition: Availability, Else: Consistency)**
    - Partition 발생 시 **가용성(Availability)**을 우선.
    - Partition이 없을 때 **일관성(Consistency)**을 유지.
    - **예시**: **DynamoDB**.
        - 네트워크 분할 상황에서는 가용성 우선.
        - 정상 상태에서는 데이터 정확성을 보장.
3. **PC/EL (Partition: Consistency, Else: Latency)**
    - Partition 발생 시 **일관성(Consistency)**을 우선.
    - Partition이 없을 때 **지연시간(Latency)**을 줄이는 데 집중.
    - **예시**: 특정 관계형 데이터베이스.
4. **PC/EC (Partition: Consistency, Else: Consistency)**
    - Partition 발생 시 **일관성(Consistency)**을 우선.
    - Partition이 없을 때도 **일관성(Consistency)**을 유지.
    - **예시**: **HBase**.
        - 항상 데이터 일관성을 유지.