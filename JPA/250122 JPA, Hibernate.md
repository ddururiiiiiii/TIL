# JPA (Java Persistence API)

- 자바에서 **객체와 관계형 데이터베이스(RDB)** 간의 매핑을 관리하기 위해 제공되는 **ORM(Object-Relational Mapping) 표준 인터페이스**
- ORM(Object-Relational Mapping) 기술을 사용해 SQL을 줄이고 객체 중심으로 개발할 수 있도록 도와줌
- JPA는 인터페이스의 모음(실제로 동작하는 코드는 없고, 인터페이스와 어노테이션만 제공)이며, 실제로 동작하는 구현체(예: Hibernate, EclipseLink, OpenJPA)가 필요함.
- JPA 표준을 따르는 한, 구현체를 다른 것으로 교체 가능.

### ORM(Object-Relational Mapping) 이란?

- **객체 지향 프로그래밍 언어에서 사용하는 객체**와 **데이터베이스에서 사용하는 테이블** 간의 **데이터 변환을 자동으로 처리해주는 기술**
- SQL을 직접 작성하지 않고도 자바 객체를 데이터베이스에 저장하거나 조회할 수 있도록 도와주는 도구

### JPA의 주요 특징

1. **객체와 데이터베이스 간의 매핑 :** 테이블을 자바 클래스에, 테이블의 컬럼을 클래스의 필드에 매핑
    - ex. `User`라는 클래스가 있다면, 이 클래스는 데이터베이스의 `user` 테이블에 매핑
2. **SQL 대신 메서드 호출 :** JPA는 객체 중심의 프로그래밍 방식으로, SQL 대신 자바 메서드 호출로 데이터를 저장, 수정, 삭제, 조회할 수 있음.
    - ex. `entityManager.persist(user);`를 호출하면 SQL `INSERT INTO user (...) VALUES (...);`가 실행됨.
3. **JPQL (Java Persistence Query Language)**
    - 객체를 대상으로 한 쿼리 언어를 제공
    - 데이터베이스 테이블이 아닌 **엔티티 클래스**를 대상으로 동작하며, SQL과 유사한 문법을 가짐.
    - 예: `SELECT u FROM User u WHERE u.name = :name`
4. **트랜잭션 관리**
    - JPA는 데이터 변경 작업이 반드시 트랜잭션 안에서 실행되도록 보장 → 트랜잭션 관리를 통해 데이터 일관성을 유지함.
5. **캐싱**
    - JPA는 1차 캐시(EntityManager)와 2차 캐시(JPA 구현체 제공)를 사용하여 데이터를 메모리에 저장하고, 데이터베이스와의 불필요한 상호작용을 줄임.
6. **지연 로딩(Lazy Loading)과 즉시 로딩(Eager Loading)**
    - 연관된 데이터를 가져오는 시점을 설정할 수 있음.
    - Lazy: 필요한 순간에 데이터를 가져옴.
    - Eager(이거): 연관된 데이터를 한 번에 전부 가져옴.

<aside>
💡

- 하이버네이트 : JPA는 명세, 하이버네이트는 구현체. / 둘의 표현기술은 똑같은데 근데 이름만 다름. 용어를 정리하기. 어떤게 다른지. 엔터티가 뭔지, 엔터티 매니저 (쓰레드 세이프 관련 매니저/팩토리, 프록시패턴 같이), 엔티티생명주기
</aside>

# 하이버네이트 (Hibernate)

- **JPA의 구현체 중 하나**: Hibernate는 JPA 표준을 구현한 ORM 프레임워크입니다.
- 단, Hibernate는 JPA를 구현할 뿐만 아니라 JPA 표준을 넘는 많은 **추가 기능**을 제공합니다.
- JPA 인터페이스를 구현하여 실제로 동작
- **Hibernate만의 특징**
    - **HQL (Hibernate Query Language)**: JPA의 JPQL과 유사한 기능 제공.
    - **2차 캐시 지원**: 더 강력한 캐싱 전략 제공 (예: Ehcache, Infinispan).
    - **다양한 데이터베이스 지원**: JPA 표준 외에도 여러 데이터베이스 기능을 활용 가능.
    - **고급 기능**: 배치 처리, 다대다 관계 최적화, 엔티티 그래프 등.
- JPA는 기본적으로 EntityManager를 사용하고, **Hibernate**는 JPA 표준을 지원하면서도 **Session**을 사용해 독자적인 API를 제공

### **EntityManager (JPA)와 Session (Hibernate)의 주요 메서드 비교**

| 기능 | JPA (EntityManager) | Hibernate (Session) |
| --- | --- | --- |
| 엔티티 저장 | `persist()` | `save()` 또는 `saveOrUpdate()` |
| 엔티티 수정 (병합) | `merge()` | `update()` 또는 `saveOrUpdate()` |
| 엔티티 삭제 | `remove()` | `delete()` |
| 엔티티 조회 (즉시 로딩) | `find()` | `get()` |
| 엔티티 조회 (지연 로딩) | `getReference()` | `load()` |
| JPQL / HQL 쿼리 생성 | `createQuery()` | `createQuery()` |
| 네이티브 SQL 쿼리 생성 | `createNativeQuery()` | `createSQLQuery()` (Hibernate 5 이전), `createNativeQuery()` (Hibernate 5 이상) |
| 트랜잭션 관리 | `EntityTransaction` 사용 | `Transaction` 사용 |
| 영속성 컨텍스트 초기화 | `clear()` | `clear()` |
| 영속성 컨텍스트 플러시 | `flush()` | `flush()` |
| 엔티티가 영속 상태인지 확인 | `contains()` | `contains()` |
