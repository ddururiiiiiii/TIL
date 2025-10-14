# Spring Data JPA에서 새로운 Entity인지 판단하는 방법
- JPA는 보통 엔티티의 @Id 값이 null이면 새 Entity라고 판단함

----

## 📌 주의할 점
- ID를 직접 세팅한 경우 : id가 있지만 DB에 없는 경우도 있어서 Spring Data JPA는 UPDATE 시도하다가 실패할 수 있음.
- ID가 Long이고, 0으로 설정된 경우 : 보통 JPA는 null만 새 Entity로 봐. 0은 이미 존재한다고 볼 수 있음 → 예상과 다르게 작동할 수 있음!
- 엔티티가 @GeneratedValue일 경우 : id 필드는 persist 전에 null이어야 하고, JPA가 자동으로 값을 생성함.

