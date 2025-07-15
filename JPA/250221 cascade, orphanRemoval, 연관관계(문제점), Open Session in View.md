## **Cascade (영속성 전이)**

- 특정 엔티티에 대한 작업(예: 저장, 삭제 등)이 연관된 다른 엔티티에도 전이되도록 설정하는 기능
    - 부모(Parent)를 저장하거나 삭제할 때, **자동으로 자식(Child)도 저장하거나 삭제**
- 연관된 엔티티를 함께 관리해야 할 때, 별도의 코드 없이 JPA가 자동으로 처리하도록 하기 위해 사용
- ex. 게시글을 삭제되면 댓글도 자동 삭제
- 연관관계가 없는 다른 자식까지 영향을 미칠 위험이 있음.
- 연관된 엔티티를 자동으로 처리하기 때문에 코드가 간결해지고 데이터의 일관성을 유지 시켜주는 장점이 있음.
- 부모 자식간의 생명주기는 다르지만, 부모 작업이 자식에게 전이 되어야 할 때

### 주요 옵션

1. **CascadeType.PERSIST**
    - 부모 엔티티를 저장할 때, 연관된 자식 엔티티도 함께 저장
2. **CascadeType.MERGE**
    - 부모 엔티티를 병합할 때, 연관된 자식 엔티티도 함께 병합
3. **CascadeType.REMOVE**
    - 부모 엔티티를 삭제할 때, 연관된 자식 엔티티도 함께 삭제
4. **CascadeType.ALL**
    - PERSIST, MERGE, REMOVE 등 모든 작업을 전이
5. **CascadeType.REFRESH**
    - 부모 엔티티를 새로 고침할 때, 자식 엔티티도 새로 고침
6. **CascadeType.DETACH**
    - 부모 엔티티를 영속성 컨텍스트에서 분리할 때, 자식 엔티티도 분리

### 예제

```java
@Entity
class Parent {
    @OneToMany(mappedBy = "parent", cascade = CascadeType.ALL)
    private List<Child> children = new ArrayList<>();
}
```

---

### **Orphan Removal (오펀, 고아 객체 제거)**

- **OrphanRemoval**은 부모 엔티티와의 관계가 끊어진 자식 엔티티를 자동으로 삭제하는 기능
- 부모와 자식 간의 관계에서 **자식 엔티티가 더 이상 부모 엔티티와 연관이 없을 경우 자동으로 삭제**되도록 처리함.
    - 부모 엔티티에서 자식 엔티티를 제거(`children.remove(child)`)하면, JPA가 자식 엔티티를 데이터베이스에서 삭제
- 부모와 자식 간의 관계가 강하게 묶여있고, 자식이 부모 없이는 존재 의미가 없을 때 사용 → 부모와 자식의 생명 주기가 동일한 경우에 적합
- ex. 앨범에서 사진이 제거되면, 사진은 더이상 데이터베이스에 남아있을 이유가 없음.

### 설정 방법

```java
@Entity
class Parent {
    @OneToMany(mappedBy = "parent", orphanRemoval = true, cascade = CascadeType.ALL)
    private List<Child> children = new ArrayList<>();
}
```

### 둘 다 같이 사용하는 경우?

- 부모 자식 관계가 매우 강하게 연결되어 있고, 부모 삭제시 자식도 삭제, 부모와 관계가 끝기면 자식도 삭제 일 때
- 1) `orphanRemoval`만 사용하는 경우 : 부모와 자식이 강하게 묶여 있지만, 부모와 저장/삭제는 자식과 관계없이 독립적으로 관리되어야 할 때

### 나의 정리

- `CascadeType.ALL`: 부모의 작업(저장, 삭제)을 자식에게 전이.
- `orphanRemoval`: 부모와 관계가 끊어진 자식을 자동으로 삭제.
- CascadeType.ALL는 부모와 자식간의 관계를 맺는 역할(전이)을 하고 orphanRemoval은 부모와 자식관의 관계를 끊었을 때 정리하는 역할을 하는 것 같다. 그래서 둘을 함께 사용하고 상호보완적임.

---

### **연관관계 맺는 방법들과 각자의 문제점**

### (1) 단방향 연관관계

- 한쪽 엔티티에서만 연관관계를 설정

```java
@Entity
class Order {
    @ManyToOne
    private Customer customer;
}
```

- **장점**: 단순하고, 매핑 관계가 명확
- **문제점** : 반대쪽 방향에서 데이터를 조회하려면 추가 쿼리가 필요

### (2) 양방향 연관관계

- 양쪽 엔티티에서 서로를 참조

```java
@Entity
class Order {
    @ManyToOne
    private Customer customer;
}

@Entity
class Customer {
    @OneToMany(mappedBy = "customer")
    private List<Order> orders = new ArrayList<>();
}
```

- **장점** : 양쪽 방향에서 데이터를 쉽게 조회할 수 있음

### **1) N+1 문제**

- **현상** : 연관된 엔터티를 조회할 때 추가적인 쿼리가 반복적으로 실행되어 성능 저하.
    - 여기서 추가적인 쿼리는 자식 쿼리들을 말함.
- **원인**
    - JPA의 기본 전략인 지연 로딩(Lazy Loading) 때문에 (지연로딩 : 연관된 엔터티를 실제로 사용할 때까지 DB에서 조회하지 않는 방식)
    - JPQL 사용 시 적절한 Fetch Join을 하지 않을 경우 발생.

예를 들어, 회원(Member) 과 주문(Order) 테이블 (회원 한 명이 여러 개의 주문을 가질 수 있는 관계 → 1:N 관계)
```java
-- 1. 먼저 모든 회원 조회 (1번 실행)
SELECT * FROM member;

-- 2. 각 회원의 주문을 따로 조회 (회원 수만큼 실행됨 → N번 실행)
SELECT * FROM order WHERE member_id = ?;
SELECT * FROM order WHERE member_id = ?;
SELECT * FROM order WHERE member_id = ?;
...
```
-> 즉, 회원 수(N)만큼 추가로 주문을 조회하는 쿼리가 실행


### **해결 방법**
1. **Fetch Join 사용**:
    
    ```java
    List<Order> orders = em.createQuery(
        "SELECT o FROM Order o JOIN FETCH o.customer", Order.class).getResultList();
    ```
    
- `JOIN FETCH`를 사용하여 **`User`와 `Order`를 한 번에** 조회하면, 추가적인 쿼리 없이 데이터를 가져올 수 있음 이 방식은 **즉시 로딩(Eager Loading)**처럼 동작
- 페이징쿼리를 함께 사용할 경우 오류가 발생할 수 있음 → 페치조인은 연관 데이터를 한번에 가져옴으로 JPA가 페이징 처리 기능과 출동할 수 있고 페이징 쿼리가 데이터베이스가 아닌 메모리에서 수행되므로 성능이 저하됨.

1. **즉시 로딩(Eager Loading) 사용**:
    
    ```java
    @ManyToOne(fetch = FetchType.EAGER)
    private Customer customer;
    ```
    

1. **`@Fetch(FetchMode.JOIN)`** 사용 (Hibernate 전용)
- Hibernate에서는 `@Fetch` 어노테이션을 사용하여 **`JOIN FETCH`*와 유사하게 설정할 수 있음.

```java
@OneToMany(fetch = FetchType.LAZY)
@Fetch(FetchMode.JOIN)
private List<Order> orders;

```

1. **`@EntityGraph` 사용 (JPA 2.1 이상) / 엔티티 그래프**
    - `@EntityGraph`는 **쿼리 성능을 최적화**하는 방법으로, 특정 연관 관계를 **미리 로드**할 수 있음.
    
    ```java
    EntityGraph<User> graph = em.createEntityGraph(User.class);
    graph.addSubgraph("orders"); // orders를 함께 조회
    
    List<User> users = em.createQuery("SELECT u FROM User u", User.class)
                          .setHint("javax.persistence.loadgraph", graph)
                          .getResultList();
    
    ```
    

1. 배치 크기 설정 (배치 사이즈 설정)
- 지연 로딩 시 JPA가 연관된 엔티티를 한꺼번에 가져오도록 하는 방법
- 추가 쿼리를 여러 번 발생시키는 대신, **배치 크기만큼 묶어서 가져오는 방식**으로 쿼리 실행 수를 줄임.
- 배치 크기를 `10`으로 설정하면, JPA는 한 번에 10개의 연관된 엔티티를 가져오는 방식. (100개에 대해서 10개씩 배치로 가져와서 총 11번의 쿼리가 실행되는 방식)
- N+1문제를 완화하면서도 페이징과 함게 사용할 수 있음.
- 데이터베이스에 부담을 주지 않는 범위에서 효율적으로 동작함.
- 페치 조인 만큼 효율적이진 않고, 적절한 배치 크기를 설정해야 함.

---

### **2) 무한 루프 문제**

- **객체 그래프를 출력할 때 객체들이 서로를 참조하면서 무한히 반복되는 현상으로,** 주로 **자기 참조**나 **양방향 연관 관계**에서 발생 (`toString`, `equals`, `hashCode` 등에서 양쪽이 서로 참조할 경우 무한 루프가 발생)
- 해결방법
    - `@JsonIgnore` (제이슨 이그노어)사용 (Jackson 라이브러리) : 직렬화 시 **순환 참조**를 막을 수 있음.
    - **`@JsonManagedReference`(앞쪽 객체)와 `@JsonBackReference` (뒤쪽 객체)사용 (Jackson 라이브러리)**
    - DTO(Data Transfer Object) 사용 : 엔터티를 직접 출력하지 않고, **DTO**를 사용하여 필요한 데이터만 전송

```java
@Entity
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String product;

    @ManyToOne
    @JoinColumn(name = "user_id")
    @JsonIgnore  // 직렬화 시 User를 제외
    private User user;
}
```

```java
@Entity
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String product;

    @ManyToOne
    @JoinColumn(name = "user_id")
    @JsonBackReference  // 순환 참조 방지
    private User user;
}
```

### **3) 양방향 연관관계 데이터 동기화**

- **설명** : 양방향 연관관계를 설정할 때, 한쪽 방향만 값이 설정되면 데이터 불일치가 발생.
- **해결 방안** : 연관관계 편의 메서드를 작성:

```java
public void addOrder(Order order) {
    orders.add(order);
    order.setCustomer(this);
}
```

### 4) 연관관계 주인을 설정하지 않을 때 문제 발생

1. 중간 테이블 생성 : JPA는 주인을 설정하지 못하면 중간 테이블(Join Table)을 생성하여 연관 관계를 관리하려고 시도
2. 중복 외래키 생성 : 양쪽 모두에서 외래 키를 관리하려고 하여, 데이터의 일관성과 성능 문제가 발생.
3. 의도와 다른 동작 : 양방향 연관관계의 장점을 잃고, 예기치 않은 SQL 쿼리나 데이터베이스 설계가 발생. (중간 테이블이 생겨서 의도치 않은 쿼리가 더 나가게 됨.\)

### **설계 추천 가이드**

1. **단방향 연관관계 추천 상황**
    - 한쪽 방향에서만 데이터를 주로 사용할 때.
    - 연관 데이터의 조회 빈도가 적거나 간단한 설계가 필요할 때.
2. **양방향 연관관계 추천 상황**
    - 양쪽 데이터를 모두 자주 조회하거나 관계가 강하게 연결된 경우.
    - 복잡한 비즈니스 로직에서 양방향 조회가 필요한 경우.
3. **실무 팁**
    - 연관관계를 무조건 양방향으로 만들지 말고, **단방향을 기본으로 설계**.
    - 필요한 경우에만 양방향 관계를 추가하고, 주인을 명확히 설정.
    - 성능과 유지보수를 고려해 `@OneToMany`보다 `@ManyToOne`을 우선 사용.

### 성능과 유지보수를 고려해 `@OneToMany`보다 `@ManyToOne`을 우선 사용하는 이유

### 성능 측면

- `@ManyToOne`은 단순한 외래 키 조회로 데이터 접근이 가능하므로 **조인 없이도 효율적으로 동작**
- 반면, `@OneToMany`는 조회 시 조인이 발생하고, 데이터가 많아질수록 성능 저하가 발생할 가능성이 큼.

### 유지보수 측면

- `@ManyToOne`은 연관관계 주인이므로 설정과 관리가 간단하며, 코드의 복잡도가 낮음.
- `@OneToMany`는 주인이 아니므로, 연관관계 설정 시 데이터 동기화 코드가 필요하고, 추가적인 설계가 요구.

---

### 4. **오픈 세션 인 뷰(Open Session in View)**

- **트랜잭션 범위를 확장**하여 뷰(HTML 렌더링) 단계까지 영속성 컨텍스트를 열어두는 방식 (원래는 서비스 계층까지 트랜잭션 범위)
- 스프링 부트에서는 기본적으로 활성화되어 있음.
- 컨트롤러에서 데이터를 조회한 후, 뷰를 렌더링할 때도 영속성 컨텍스트가 열려 있어 **지연 로딩(Lazy Loading)**이 가능

### 장점

1. **지연 로딩(Lazy Loading) 지원 :** 뷰에서 연관된 데이터를 조회할 때 추가 쿼리를 실행할 수 있음.
2. **코드 간결화 :** 서비스 계층에서 Lazy Loading 데이터를 강제로 가져올 필요 없이, 뷰 단계에서 필요한 시점에 자동으로 데이터를 로드할 수 있음.

### 단점 및 문제점

1. **N+1 문제 :** 지연 로딩으로 인해 예상치 못한 다수의 쿼리가 실행될 수 있음.
2. **트랜잭션 확장 :** 트랜잭션이 뷰 단계까지 열려 있어 DB 연결을 오랫동안 점유하게 됨.
3. Open Session in View는 간단한 프로젝트에서는 편리하지만, 복잡한 환경에서는 **비활성화**를 고려 → 대신, **서비스 계층에서 필요한 데이터를 모두 미리 로드(DTO 사용)**하는 방식으로 해결
4. **예상치 못한 LazyInitializationException :** Open Session in View가 비활성화된 경우, 영속성 컨텍스트가 닫힌 뒤 지연 로딩을 시도하면 예외가 발생.

### 비활성화 방법 (스프링 부트 기준)

```yaml
spring:
  jpa:
    open-in-view: false
```

<br>

----

<br>

# @OneToOne 연관관계에서 Lazy Loading을 설정할 때 주의할 점
- 자바 ORM (JPA)에서는 기본적으로 OneToOne은 EAGER이다 (즉시로딩) → 엔티티를 조회하면 연관된 것도 무조건 같이 가져와버림.
- @OneToOne(fetch = FetchType.LAZY) 이렇게 작성하면 LAZY로딩이 되지만...!

### 진짜로 LAZY가 안될 수 있음
- @OneToOne은 기본적으로 프록시를 써서 지연 로딩하려고 함
- 근데 프록시 객체가 제대로 동작하려면 해당 연관 필드가 접근될 때까지 초기화가 안 돼 있어야 함.
- 그런데 실무에서는 보통 OneToOne 대상 필드가 null 검사되는 순간 프록시가 터져버림
~~~ java
if (user.getProfile() != null) { ... } // 이 순간 프록시 초기화되어버림!
~~~

- JPA 구현체에 따라 (Hibernate 기준) 실제로는 OneToOne + LAZY는 프록시 대신 즉시 로딩이 될 수도 있음 (JPA 스펙 구멍임)
- 그래서 bytecode enhancement 또는 @LazyToOne(LazyToOneOption.NO_PROXY) 같은 걸 써야 진짜 지연로딩이 되기도 함

### 정리
| 주의할 점                     | 설명                                                     |
| ------------------------- | ------------------------------------------------------ |
| 기본은 EAGER                 | `@OneToOne`은 기본으로 즉시 로딩이라 성능에 악영향 가능                   |
| LAZY 설정해도 제대로 동작 안 할 수 있음 | 프록시가 강제로 초기화되거나, Hibernate가 내부적으로 EAGER로 처리하는 경우도 있음   |
| `null` 검사만 해도 로딩 발생       | `getProfile() != null` 해버리면 이미 DB 접근함                  |
| 진짜로 LAZY 원하면 추가 설정 필요     | `@LazyToOne`, `bytecode enhancer`, `@MapsId` 같은 걸 써야 함 |


- @OneToOne(fetch = LAZY)는 진짜 지연 로딩이 안 될 수 있으니 무조건 믿지 마라!
- 성능 민감한 코드에서는 꼭 로깅 찍어보거나 Hibernate 설정도 확인해봐야 함!