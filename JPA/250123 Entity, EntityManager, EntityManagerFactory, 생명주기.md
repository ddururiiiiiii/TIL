## **1. 엔터티(Entity)**

- **데이터베이스의 테이블에 매핑되는 자바 클래스**
- JPA를 사용하면 SQL 대신 자바 객체를 통해 데이터베이스 작업을 처리

### **엔터티의 주요 요구사항**

1. **필수 조건**:
    - `@Entity` 어노테이션이 붙어야 함.
    - 기본 생성자가 있어야 함.
    - `@Id` 어노테이션으로 **식별자(primary key)**를 지정해야 함.
2. **선택 조건** : `@Table` 어노테이션으로 테이블 이름을 지정 가능.

### **예시**

```java
import jakarta.persistence.*;

@Entity
@Table(name = "users") // users 테이블과 매핑
public class User {
    @Id // primary key
    @GeneratedValue(strategy = GenerationType.IDENTITY) // 자동 증가
    private Long id;

    @Column(nullable = false) // not null 제약조건
    private String name;

    private String email;

    // 기본 생성자 (필수)
    public User() {}

    // Getter와 Setter
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }

    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
}

```

---

## **2. 엔터티 매니저 (EntityManager)**

- **JPA에서 엔터티를 관리하는 핵심 객체**
- 엔터티의 생명주기를 관리하고, 데이터베이스와 상호작용을 담당

### **EntityManager의 주요 역할**

1. **CRUD 작업**: 엔터티를 데이터베이스에 **저장**, **수정**, **삭제**, **조회**.
2. **JPQL 실행**: JPQL(Java Persistence Query Language) 쿼리를 실행.
3. **영속성 컨텍스트 관리**: 엔터티를 영속성 컨텍스트에 저장하고 추적(변경 감지)함.

### 엔티티 매니저와 쓰레드 세이프 관계

- 트랜잭션 단위로 동작하며, 스레드 세이프 하지 않음. 엔티티 매니저는 영속성 컨텍스트를 관리하고 영속성 컨텍스트는 특정 스레드에서만 사용되도록 설계 되었기 때문 (한 스레드가 변경한 엔티티를 다른 스레드가 삭제하려고 하면 데이터가 꼬일 수 있음.)
- 한 스레드에서만 사용하도록 설계 되어 있어 한 트랜잭션 내에서 한 스레드만 사용해야 함 → 여러 스레드에서 동시에 엔티티 매니저를 사용하면 데이터가 손상되거나 동기화, 일관성 문제가 발생할 수 있음.
- 트랜잭션 범위를 관리하기 위해서 프록시를 사용하고 프록시는 실제 엔티티 매니저를 동적으로 생성, 관리, 종료함.

### 엔티티 매니저를 쓰레드 세이프하게 사용하는 법

- 스레드별로 독립적인 인스턴스를 사용
1. 스레드마다 별도 `EntityManager` 생성
2. 스프링의 `@PersistenceContext` 사용 (스레드 안전 보장)
    1. `@PersistenceContext`를 통해 주입받은 `EntityManager`는 내부적으로 **스레드마다 독립적으로 관리**
3. 스프링 부트에서 `@Transactional` 사용
    1. 스프링의 `@Transactional`은 트랜잭션 시작 시 `EntityManager`를 자동으로 생성하고, 트랜잭션 범위가 끝나면 닫아줌.

### **EntityManager 사용 예시**

```java
EntityManagerFactory emf = Persistence.createEntityManagerFactory("my-persistence-unit");
EntityManager em = emf.createEntityManager();

em.getTransaction().begin(); // 트랜잭션 시작

User user = new User();
user.setName("Alice");
user.setEmail("alice@example.com");

em.persist(user); // 엔터티를 영속성 컨텍스트에 저장
em.getTransaction().commit(); // 트랜잭션 커밋

em.close();
emf.close();

```

---

## **3. 엔터티 매니저 팩토리 (EntityManagerFactory)**

- **EntityManager 객체를 생성하는 팩토리 객체**
- 애플리케이션 전체에서 **하나만 생성**(싱글톤 패턴)해야 성능적으로 효율적 → 무거운 객체이기 때문에 자주 생성/종료하지 않음.
- 여러 스레드가 동시에 엔티티 매니저 팩토리를 공유해도 내부에 동기화 처리가 되어 있어서 다중 스레드 환경에도 문제가 발생하지 않음.

### **역할**

1. **EntityManager 생성**: `EntityManager em = emf.createEntityManager();`
2. **애플리케이션 종료 시 리소스 정리**: `emf.close();`

### **엔터티 매니저 팩토리가 필요한 이유**

- `EntityManager`는 **트랜잭션 단위**로 사용되며, 특정 작업을 처리한 후에는 **버려야 하는 단발성 객체라서, 필요할 때마다 새로 생성**해서 사용하는 것이 일반적이기 때문에, 이를 효율적으로 생성하고 관리하는 객체가 필요하고 `EntityManagerFactory`는 `EntityManager`를 생성하는 책임을 맡음.
- 데이터베이스와의 연결 정보(드라이버, URL, 사용자 인증 등)를 초기화하는 작업은 **비용이 많이 들기 때문에** 반복적으로 하면 비효율적 → `EntityManagerFactory`는 애플리케이션 실행 시 초기화되고, **데이터베이스 연결을 설정**해두어 `EntityManager` 생성 시 빠르게 처리할 수 있도록 함
- `EntityManagerFactory`는 애플리케이션 실행 중 **공유 객체**로 사용되며, 여러 스레드가 동시에 접근할 수 있는 **스레드 세이프(Thread Safe)**한 객체 → `EntityManagerFactory`는 **리소스를 효율적으로 관리**하며, 애플리케이션 전역에서 재사용
  
  
---

## **4. 엔터티의 생명주기 (Entity Life Cycle)**

- 엔터티는 JPA에서 **4가지 상태**를 가짐.

| 상태 | 설명 |
| --- | --- |
| **비영속(New)** | 엔터티가 영속성 컨텍스트에 관리되지 않는 상태. (DB와 연관 없음) |
| **영속(Managed)** | 엔터티가 영속성 컨텍스트에 저장되어 관리되는 상태. (DB와 동기화) |
| **준영속(Detached)** | 영속성 컨텍스트에서 분리된 상태. (DB와 동기화되지 않음) |
| **삭제(Removed)** | 엔터티가 삭제 예약된 상태. (트랜잭션 커밋 시 실제 삭제) |

---

### **1) 비영속 (New)**

- 단순히 객체를 생성한 상태로, JPA와 전혀 관계가 없음.
- **예시**:
    
    ```java
    User user = new User(); // 단순 객체 생성
    user.setName("Alice");
    ```
    
- **작동** : 이 상태에서는 데이터베이스에 저장되지 않음.

---

### **2) 영속 (Managed)**

- `EntityManager`를 통해 영속성 컨텍스트에 저장된 상태.
- 데이터베이스와 동기화 가능.
- JPA가 객체를 관리하며, 변경 사항을 감지하고 자동으로 업데이트.
- **예시**:
    
    ```java
    em.persist(user); // 영속 상태로 변경
    ```
    
- **작동** : `persist()`를 호출하면 객체가 영속성 컨텍스트에 저장되고, DB에 반영.

---

### **3) 준영속 (Detached)**

- 영속 상태에서 영속성 컨텍스트와 분리된 상태.
- 더 이상 JPA가 관리하지 않음.
- 변경감지가 일어나지 않고, 트랜잭션에 포함되지 않으며 다시 영속상태로 변환도 가능함.
- `detach()` , `clear()` ,  `close()` 메서드를 호출했을 때, 트랜잭션이 롤백됐을 때
- **예시**:
    
    ```java
    em.detach(user); // 준영속 상태로 변경
    ```
    
- **작동** : 영속성 컨텍스트를 사용하지 않고 직접 데이터베이스 작업을 해야 함.
- 사용하는 이유 : 성능 최적화 (필요없는 엔터티를 분리해서 영속성 컨텍스트 크기를 줄일 수 있음)

---

### **4) 삭제 (Removed)**

- 영속성 컨텍스트에서 관리되고 있는 엔티티를 삭제 요청한(예정된) 상태
- 삭제될 예정 상태이기 때문에 트랜잭션이 종료 되기 전까지는 영속성 컨텍스트에서 관리 되다가 엔티티는 **트랜잭션이 커밋될 때 데이터베이스에서 삭제되고, 영속성 컨텍스트에서도 사라짐.**
- **예시**:
    
    ```java
    em.remove(user); // 삭제 예약
    em.getTransaction().commit(); // DB에서 삭제
    ```
    

---

### **엔터티 생명주기의 흐름**

1. **New** → `persist()` → **Managed**
    - 엔터티를 영속성 컨텍스트에 저장.
2. **Managed** → `remove()` → **Removed**
    - 엔터티를 삭제 상태로 변경.
3. **Managed** → `detach()` → **Detached**
    - 영속성 컨텍스트에서 분리.
4. **Detached** → `merge()` → **Managed**
    - 다시 영속 상태로 복구.

---

### **생명주기 예제**

```java
EntityManagerFactory emf = Persistence.createEntityManagerFactory("my-persistence-unit");
EntityManager em = emf.createEntityManager();

// 1. 비영속 상태
User user = new User();
user.setName("Alice");

// 2. 영속 상태
em.getTransaction().begin();
em.persist(user); // 영속성 컨텍스트에 저장
em.getTransaction().commit();

// 3. 준영속 상태
em.detach(user); // 영속성 컨텍스트에서 분리

// 4. 삭제 상태
em.getTransaction().begin();
em.remove(user); // 삭제 예약
em.getTransaction().commit();

em.close();
emf.close();

```
