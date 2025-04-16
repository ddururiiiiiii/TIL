# @Transactional

- 데이터베이스 트랜잭션을 관리하기 위한 애노테이션
- 코드에서 명시적으로 트랜잭션을 시작하거나 종료할 필요 없이 스프링 컨테이너가 트랜잭션을 관리
- 스프링에서 @Transactional을 사용하면, 명시적으로 설정하지 않은 옵션(격리수준, 자동커밋)은 데이터베이스의 기본 설정에 의존함.
    - ex. @Transactional을 사용할 때 특별히 격리 수준을 설정하지 않으면, MySQL의 기본 격리 수준인 REPEATABLE READ를 사용
  
## **@Transactional 특징**

### **1. 프록시 기반 트랜잭션 관리**

- 프록시는 `@Transactional`이 선언된 메서드를 감싸서 트랜잭션 시작, 커밋, 롤백을 처리.
- **제한점**
    - 프록시는 **외부에서 호출**될 때만 트랜잭션 규칙을 적용하고, **동일 객체 내에서 호출된 메서드**에 대해서는 트랜잭션을 적용하지 않음(프록시를 거치지 않고, 자기 자신(this)을 직접 호출) →  **자기 호출(self-invocation)** 문제
    - 프록시는 **public 메서드** 호출만 감지 → `private` 메서드는 프록시 객체의 대상이 될 수 없음.
    → @Transactional도 결국 AOP로 동작하고 AOP는 프록시 기반임. 프록시는 ‘대리호출’을 기반으로 하고 private이거나 내부 호출이면 당연히 대리호출이 불가능함.
**자기 호출(self-invocation)** 문제 **예시 코드:**

```java
@Service
public class MyService {
    @Transactional
    public void methodA() {
        // 트랜잭션 시작됨
        System.out.println("methodA 실행");
        methodB(); // 내부 호출 (this.methodB()와 동일)
    }

    @Transactional
    public void methodB() {
        // 트랜잭션 시작되지 않음
        System.out.println("methodB 실행");
    }
}
```

- **클라이언트가 `methodA` 호출**:
    - 프록시 객체를 통해 호출됨 → 트랜잭션 시작.
- **`methodA`에서 `methodB` 호출**:
    - 내부에서 `this.methodB()` 호출 → 프록시를 우회 → 트랜잭션 규칙 적용 X.

### 올바른 경우: 외부에서 호출하도록 변경

```java
@Service
public class MyService {
    private final MyService myService;

    public MyService(MyService myService) {
        this.myService = myService;
    }

    @Transactional
    public void methodA() {
        // 트랜잭션 시작됨
        System.out.println("methodA 실행");
        myService.methodB(); // 외부 호출 (프록시를 통해 호출)
    }

    @Transactional
    public void methodB() {
        // 트랜잭션 시작됨
        System.out.println("methodB 실행");
    }
}

```

1. **`methodA`가 `myService.methodB()` 호출**:
    - `myService`는 프록시 객체 → 트랜잭션 규칙 적용.
2. 이제 `methodB`도 트랜잭션 관리가 적용

- **해결 방법**:
    - 트랜잭션이 필요한 메서드는 외부 객체나 프록시를 통해 호출.
    - 또는 **AopContext.currentProxy()**를 사용해 현재 프록시 객체를 직접 호출.
        
        ```java
        java
        코드 복사
        import org.springframework.aop.framework.AopContext;
        
        @Transactional
        public void methodA() {
            ((MyService) AopContext.currentProxy()).methodB();
        }
        
        ```
        

### **2. 클래스 레벨 vs 메서드 레벨**

- **클래스 레벨 @Transactional**: 클래스 내 모든 메서드에 트랜잭션이 적용.
- **메서드 레벨 @Transactional**: 특정 메서드에만 트랜잭션 적용.
- **우선순위** : 메서드 레벨 설정이 클래스 레벨 설정을 덮어씀.

### **3. 롤백 정책**

- 기본적으로 `RuntimeException`과 `Error`만 롤백 대상
- 특정 **체크 예외(Checked Exception)**에 대해서도 롤백하려면 `rollbackFor`를 사용해야 함.

**예시:**

```java
@Transactional(rollbackFor = MyCheckedException.class)
public void myMethod() throws MyCheckedException {
    // MyCheckedException 발생 시 롤백
}
```

### 4. 스레드 로컬(ThreadLocal)

- 스프링의 트랜잭션은 주로 **스레드 로컬(ThreadLocal)**을 사용하여 현재 스레드에서 처리되는 트랜잭션을 추적하고 관리

#### **스레드 로컬 (ThreadLocal)**:

- 트랜잭션 관리는 각 **스레드별로 독립적임.** 즉, 한 스레드에서 시작된 트랜잭션은 해당 스레드에서만 유효하며, 다른 스레드에서 같은 트랜잭션을 사용할 수 없음.
- `@Transactional`이 적용된 메소드가 호출되면, 스프링은 **현재 스레드**에 대한 트랜잭션을 시작. 이 트랜잭션은 스레드가 종료될 때까지 유지되며, 다른 스레드와는 독립적으로 동작.

#### **멀티 스레드 환경에서의 주의사항**:

- **동시성 문제**: 여러 스레드가 동시에 트랜잭션을 처리할 경우, 트랜잭션 간의 **경합 조건(race condition)**이나 **데이터 불일치** 문제가 발생할 수 있음. 이를 방지하기 위해 트랜잭션의 **격리 수준**을 적절히 설정하고, **동기화**를 고려해야 해야 함.

#### **실제 예시와 동기화**

- 가령, 두 스레드가 각각 트랜잭션을 처리하고 있을 때, 트랜잭션 격리 수준이 낮다면 한 스레드에서 변경한 데이터가 다른 스레드에서 바로 읽히는 문제가 발생할 수 있음. 이를 방지하려면 격리 수준을 높여 **트랜잭션 간 동기화**를 강제할 수 있음.


-----


## **전파 속성 (Propagation)**

- 트랜잭션이 메서드 간 호출 시 어떻게 동작할지 설정
- 많이 안씀.
- 대부분 REQUIRED를 쓰지만 REQUIRES_NEW을 실무에서 써 봄직함.
  
**전파 속성 종류:**

1. **REQUIRED (리콰이어드, 기본값)**
- 이미 트랜잭션이 있으면 해당 트랜잭션을 사용, 없으면 새로 생성.
- **예시**: 대부분의 비즈니스 로직에서 사용.

1. **REQUIRES_NEW**
- 항상 새로운 트랜잭션을 시작하며, 기존 트랜잭션은 보류(일시 중단).
- **예시**: 독립적으로 처리되어야 하는 작업, 로그 기록.

1. **SUPPORTS**
- 트랜잭션이 있으면 참여하고, 없으면 비트랜잭션으로 실행.
- **예시**: 트랜잭션 필요 여부가 중요하지 않은 경우.

1. **MANDATORY (맨더토리)**
- 반드시 기존 트랜잭션에서 실행, 없으면 예외 발생.
- **예시**: 항상 상위 트랜잭션이 존재해야 하는 경우.

1. **NOT_SUPPORTED**
- 트랜잭션을 사용하지 않음. 기존 트랜잭션은 보류.
- **예시**: 트랜잭션이 필요 없는 작업.

1. **NEVER**
- 트랜잭션 없이 실행, 기존 트랜잭션이 있으면 예외 발생.
- **예시**: 트랜잭션과 충돌이 나는 작업.

1. **NESTED**
- 현재 트랜잭션 내부에서 중첩 트랜잭션 시작.
- **예시**: 상위 트랜잭션의 롤백과 별개로 중첩된 작업의 롤백/커밋 관리.

```java
@Transactional(propagation = Propagation.REQUIRED)
public void parentMethod() {
    childMethod(); // 같은 트랜잭션 사용
}

@Transactional(propagation = Propagation.REQUIRES_NEW)
public void childMethod() {
    // 새로운 트랜잭션 시작
}
```

----

## **격리 수준 (Isolation)**

- 격리 수준은 동시에 여러 트랜잭션이 실행될 때, 데이터의 일관성과 정합성을 어떻게 유지할지 정의
- 격리 수준에는 4가지 주요 단계가 있으며, 낮은 단계일수록 데이터 충돌 가능성이 높아짐

1. **DEFAULT :** 데이터베이스의 기본 격리 수준을 따름.
2. **READ_UNCOMMITTED :** 다른 트랜잭션의 **커밋되지 않은 변경사항**도 읽을 수 있음.
    - **문제점**: Dirty Read 발생 가능.
3. **READ_COMMITTED (기본값):** 다른 트랜잭션의 **커밋된 변경사항**만 읽을 수 있음.
    - **문제점**: Non-Repeatable Read 가능.
4. **REPEATABLE_READ :** 트랜잭션이 시작된 시점 이후의 데이터를 읽음(반복 가능).
    - **문제점**: Phantom Read 가능.
5. **SERIALIZABLE : 가**장 높은 수준의 격리, 트랜잭션이 순차적으로 실행.
    - **문제점**: 격리수준이 너무 높으면 동시에 처리할 수 있는 트랜잭션의 수가 줄어들어 (락)성능 저하 가능.

## **주요 문제 유형**

### **Dirty Read (더티 리드)**

- **발생 가능 수준**: **READ_UNCOMMITTED**
- **설명**: 한 트랜잭션이 아직 커밋하지 않은 데이터를 다른 트랜잭션이 읽을 수 있음.
- **문제 예시**:
    - 트랜잭션 A: "고객 계좌 잔액을 100에서 50으로 수정 (미완료)"
    - 트랜잭션 B: "고객 계좌 잔액을 읽음 → 50으로 읽음"
    - 트랜잭션 A가 롤백 → 데이터는 여전히 100이지만 트랜잭션 B는 잘못된 값을 사용.

### **Non-Repeatable Read (비반복 읽기)**

- **발생 가능 수준**: **READ_COMMITTED**
- **설명**: 한 트랜잭션이 동일한 데이터를 두 번 읽었을 때, 그 사이에 다른 트랜잭션이 데이터를 수정하면 읽은 값이 달라짐.
- **문제 예시**:
    - 트랜잭션 A: "계좌 잔액을 읽음 → 100"
    - 트랜잭션 B: "계좌 잔액을 150으로 수정하고 커밋"
    - 트랜잭션 A: "다시 계좌 잔액을 읽음 → 150" (값이 달라짐)

### **Phantom Read (팬텀 리드)**

- **발생 가능 수준**: **REPEATABLE_READ**
- **설명**: 한 트랜잭션이 조건에 따라 데이터를 조회했을 때, 다른 트랜잭션이 데이터를 추가하거나 삭제하면 첫 번째 트랜잭션의 결과가 달라질 수 있음.
- **문제 예시**:
    - 트랜잭션 A: "잔액 100 이상의 고객 목록 조회 → 고객 1, 고객 2"
    - 트랜잭션 B: "잔액 100 이상의 고객 추가 (고객 3) 후 커밋"
    - 트랜잭션 A: "다시 고객 목록 조회 → 고객 1, 고객 2, 고객 3" (팬텀 데이터 발생)

## **각 격리 수준과 발생 가능한 문제 요약**

| 격리 수준 | Dirty Read | Non-Repeatable Read | Phantom Read | 성능 |
| --- | --- | --- | --- | --- |
| **READ_UNCOMMITTED** | 가능 | 가능 | 가능 | 매우 빠름 |
| **READ_COMMITTED** | 불가능 | 가능 | 가능 | 빠름 |
| **REPEATABLE_READ** | 불가능 | 불가능 | 가능 | 중간 |
| **SERIALIZABLE** | 불가능 | 불가능 | 불가능 | 느림 |

## **쉽게 이해하기**

### **Dirty Read**

- 당신이 은행 계좌를 조회했는데, 아직 확정되지 않은 임시 데이터를 본 경우. 나중에 그 데이터가 취소되더라도 이미 잘못된 결정을 내릴 수 있음.

### **Non-Repeatable Read**

- 식당 메뉴를 두 번 읽었는데, 두 번째 읽을 때 메뉴 가격이 바뀌어 있음.

### **Phantom Read**

- 첫 번째 조회에서 '서울에 있는 카페 목록'을 보았는데, 다른 누군가가 카페를 추가해서 두 번째 조회에서는 새로운 카페가 목록에 나타남.


## 그외 속성들

1. **readOnly (읽기 전용)**
- **설명**: 트랜잭션이 데이터의 **조회 작업만 수행할 때** 설정하는 속성.
- **기능**:
- 데이터 변경 작업(INSERT, UPDATE, DELETE)을 방지.
- 데이터베이스 드라이버나 JPA 구현체(예: Hibernate)에서 최적화를 수행할 수 있음.
- **장점**:
- 성능 향상: 변경 작업이 없다는 힌트를 제공하므로 내부적으로 최적화를 수행.
- 실수 방지: 코드에서 데이터 변경 작업이 발생하면 예외가 발생하도록 설정 가능.
- **주의**
    - readOnly = true는 읽기 전용 트랜잭션을 선언하지만, 데이터베이스에서 강제적으로 변경 작업을 막지는 않음.
    - JPA와 같은 ORM에서만 예외가 발생하며, SQL을 직접 사용할 경우 변경 작업이 수행될 수 있음.

```sql
@Transactional(readOnly = true)
public List<User> getUsers() {
    return userRepository.findAll();
}
```

2. **timeout (타임아웃)**
- **설명**: 트랜잭션이 지정된 시간 안에 완료되지 않으면 **롤백**하도록 설정.
- **기능**:
    - 특정 작업이 오래 걸릴 경우 이를 제한하여 시스템의 성능을 보호.
    - 기본값은 -1로, 타임아웃 제한이 없음. / **단위**: 초(seconds).

```sql
@Transactional(timeout = 5) // 5초 안에 트랜잭션 완료 필요
public void processLargeData() {
    // 5초 안에 끝나지 않으면 롤백
}
```

**3. rollbackFor (롤백 조건)**

- **설명**: 특정 예외가 발생했을 때만 트랜잭션을 롤백하도록 설정.
- 스프링은 **RuntimeException**과 **Error**에서만 트랜잭션을 롤백.
- **Checked Exception**은 기본적으로 롤백되지 않음.
- **사용 예시**:

```sql
@Transactional(rollbackFor = CustomException.class)
public void saveData() throws CustomException {
    // CustomException 발생 시 롤백
}
```

**4. noRollbackFor (롤백 제외 조건)**

- **설명**: 특정 예외가 발생해도 트랜잭션을 롤백하지 않도록 설정.
- **사용 예시**:

```sql
@Transactional(noRollbackFor = CustomException.class)
public void saveData() throws CustomException {
    // CustomException 발생해도 롤백하지 않음
}
```

**6. combined example**

여러 속성을 함께 설정할 수도 있음.

```sql
@Transactional(
    propagation = Propagation.REQUIRED,
    readOnly = false,
    timeout = 10,
    isolation = Isolation.SERIALIZABLE,
    rollbackFor = {CustomException.class}
)
public void performComplexTransaction() {
    // 트랜잭션 동작
}
```


# **@Transactional 사용 시 발생할 수 있는 또 다른 문제점**

## **LazyInitializationException**

- JPA에서 연관 관계가 설정된 엔티티(예: `@OneToMany`, `@ManyToOne`)를 `LAZY` 로딩 방식으로 설정한 경우에 발생.
- `@Transactional` 메서드가 종료되면 **영속성 컨텍스트(EntityManager)**가 닫히는데 **트랜잭션이 끝난 후 연관된 데이터에 접근**하려 하면 지연로딩 상태라서 실제 데이터를 가져오지 않았음으로 LazyInitializationException이 발생.
    - ex. 물건(엔티티 데이터)을 가져오려면 가게(EntityManager)가 열려 있어야 하는데, 이미 문이 닫혔음.

### **해결 방법**

- **`FETCH`를 `EAGER`로 변경**: 항상 연관 데이터를 가져오도록 설정.
- **트랜잭션 내에서 데이터 초기화**: `Hibernate.initialize()`나 `getter` 호출로 필요한 데이터를 미리 로드.
- **DTO 사용**: 필요한 데이터만 조회하여 반환.

### 

## **비동기 호출과 트랜잭션**

- 비동기 방식(`@Async`)으로 실행된 메서드는 기본적으로 **새로운 쓰레드**에서 실행
- 새로운 쓰레드에서는 기존 트랜잭션 컨텍스트가 전달되지 않으므로 트랜잭션이 적용되지 않음.

### **해결 방법**

1. **트랜잭션이 필요 없는 작업으로 비동기 분리**:
    - 트랜잭션이 꼭 필요하지 않다면 비동기 작업으로 분리.
2. **트랜잭션 전파 속성 변경**:
    - `@Async` 메서드 호출 전에 트랜잭션을 미리 열어놓고 **`REQUIRES_NEW`** 전파 속성을 사용.
    
    ```java
    java
    코드 복사
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void asyncMethod() {
        System.out.println("트랜잭션 분리 실행");
    }
    
    ```
    
3. **동기 방식으로 처리**:
    - 비동기 대신 동기 방식으로 트랜잭션을 유지.
4. **프록시 객체 사용 확인**:
    - `@Async` 메서드가 **외부 프록시를 통해 호출되도록 설정**.
    - 호출 메서드와 비동기 메서드가 같은 객체에 있다면 프록시가 작동하지 않음.

### **예제 코드**

```java
java
코드 복사
@Service
public class MyService {
    @Async
    @Transactional
    public void asyncMethod() {
        // 트랜잭션이 적용되지 않음
        System.out.println("비동기 작업 실행");
    }
}

```

1. `asyncMethod`는 `@Async`로 비동기 실행.
2. 스프링은 새로운 쓰레드에서 `asyncMethod`를 실행.
3. **문제**: 새로운 쓰레드는 기존 트랜잭션 컨텍스트를 알지 못하므로 `@Transactional`이 무시.

---

# 트랜잭션의 보장범위

- 보장범위? 트랜잭션은 데이터베이스 작업의 **일관성, 원자성, 격리성, 지속성(ACID)**을 보장하는 범위 → 즉, **어디까지 하나의 트랜잭션으로 묶여 처리될지**를 결정

### **트랜잭션의 보장 범위**

1. **싱글 서버 + 단일 데이터소스**
    - 트랜잭션은 동일 데이터소스(예: 하나의 MySQL DB) 내에서만 보장.
    - 예:
        - A 테이블과 B 테이블이 **같은 데이터베이스에 있을 때** 트랜잭션이 적용 가능.
        - 다만, 서로 다른 데이터베이스 서버를 다룰 경우 기본적으로 적용되지 않음.
2. **멀티 서버(분산 환경)**
    - 서로 다른 데이터소스(예: MySQL + PostgreSQL) 또는 분산 시스템에서는 **기본 트랜잭션**으로 ACID를 보장할 수 없음.
    - 이때는 **분산 트랜잭션**이나 **사가 패턴(Saga Pattern)**과 같은 기법이 필요.

### **분산 트랜잭션이란?**

- **분산 트랜잭션**은 여러 데이터소스(예: 서로 다른 DB, 메시지 큐 등)에 걸쳐 ACID를 보장하는 트랜잭션 관리 방식.
- 스프링은 **JTA(Java Transaction API)**를 지원
- 분산 환경에서 완전한 ACID 보장은 매우 어렵고 성능 저하를 유발할 수 있어 **사가 패턴**이나 **보상 트랜잭션** 같은 비동기 방식이 많이 사용됨.

## **분산 트랜잭션 관리 방법**

### **(1) 2PC (Two-Phase Commit)**

- **두 단계 커밋**을 통해 트랜잭션을 관리.
    - **준비 단계(Prepare Phase)**: 모든 참여자(DB, 메시지 큐 등)가 작업 준비 상태인지 확인.
    - **커밋 단계(Commit Phase)**: 모두 준비 완료 시, 커밋 실행. 실패 시 롤백.
- 도구 : Atomikos, Bitronix 등 JTA 구현체.
- **문제점**: 네트워크 장애나 지연 시 성능이 크게 저하될 수 있음.

**예제**:

```java
java
코드 복사
@Bean
public PlatformTransactionManager transactionManager() {
    JtaTransactionManager transactionManager = new JtaTransactionManager();
    return transactionManager;
}

```

### **(2) 사가 패턴(Saga Pattern)**

- *ACID 대신 최종 일관성(Eventual Consistency)**을 보장.
- 트랜잭션을 작은 단계(로컬 트랜잭션)로 나누고, 실패 시 보상 작업(Undo)을 실행.
- **비유**:항공권 예약 → 호텔 예약 → 렌터카 예약.
    - 렌터카 예약 실패 시 항공권과 호텔 예약 취소(보상 트랜잭션).

**구현 방법**:

- **Orchestration(오케스트레이션)**: 중앙 컨트롤러가 단계별 트랜잭션을 관리.
- **Choreography**: 각 서비스가 이벤트를 통해 다음 단계를 진행.

**예제 (Orchestration)**:

```java
java
코드 복사
@Service
public class OrderService {
    @Transactional
    public void placeOrder(OrderRequest request) {
        // 단계별로 트랜잭션 처리
        paymentService.processPayment(request);
        inventoryService.reserveStock(request);
    }
}

```

### **(3) 메시지 큐 기반 트랜잭션**

- 메시지 큐(Kafka, RabbitMQ 등)를 활용해 트랜잭션 상태를 비동기적으로 관리.
- 메시지를 통해 이벤트를 전달하며 최종 일관성을 보장.

### **분산 트랜잭션 관리의 선택 기준**

- **ACID**가 반드시 필요한 경우 → JTA, 2PC.
- **성능과 확장성**이 중요한 경우 → 사가 패턴, 메시지 큐.