# **DELETE vs TRUNCATE vs DROP**

| **명령어** | **설명** | **특징** |
| --- | --- | --- |
| **DELETE** | 특정 행(row)을 삭제. | WHERE 절 사용 가능. 트랜잭션 롤백 가능. |
| **TRUNCATE** | 테이블의 모든 데이터를 삭제. | 트랜잭션 롤백 불가. 테이블 구조는 유지. 빠르게 대량 삭제 가능. |
| **DROP** | 테이블 자체를 삭제. | 테이블과 데이터 모두 삭제. 롤백 불가. |

### **예시**

- **DELETE**: 특정 학생 삭제.
    
    ```sql
    DELETE FROM Students WHERE StudentID = 1;
    ```
    
- **TRUNCATE**: 학생 테이블 초기화.
    
    ```sql
    TRUNCATE TABLE Students;
    ```
    
- **DROP**: 학생 테이블 삭제.
    
    ```sql
    DROP TABLE Students;
    ```
