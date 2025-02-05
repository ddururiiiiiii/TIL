# **SQL Injection**

- 사용자가 입력한 값을 통해 **악의적인 SQL 쿼리**를 실행하여 데이터베이스를 공격하는 방식.
- 공격자는 데이터 조회, 삭제, 조작 등을 할 수 있음.

### **예시**

- 사용자 입력을 그대로 SQL 쿼리에 삽입하는 경우:

```java
String query = "SELECT * FROM Users WHERE username = '" + username + "';";
```

- 공격자가 `username`에 `admin' OR '1'='1`을 입력하면:

```sql
SELECT * FROM Users WHERE username = 'admin' OR '1'='1'
```

- 이 쿼리는 항상 참이 되어, 모든 사용자 정보를 조회 가능.

---

### **SQL Injection 방어 및 방지 방법**

1. **PreparedStatement 사용**
    - 사용자 입력값을 SQL에 직접 삽입하지 않고, **바인딩 변수**를 사용.
    
    ```java
    String query = "SELECT * FROM Users WHERE username = ? AND password = ?";
    PreparedStatement pstmt = connection.prepareStatement(query);
    pstmt.setString(1, username);
    pstmt.setString(2, password);
    ResultSet rs = pstmt.executeQuery();
    
    ```
    
2. **ORM 사용 :** JPA와 같은 ORM 도구는 SQL Injection 방어 기능을 기본적으로 제공
3. **입력 값 검증 :** 사용자 입력 값에 대해 정규식을 사용해 유효성을 검증.
    
    ```java
    if (!username.matches("[a-zA-Z0-9]+")) {
        throw new IllegalArgumentException("Invalid username");
    }
    
    ```
    
4. **최소 권한 원칙 :** 데이터베이스 사용자를 애플리케이션에서 필요한 최소 권한으로 설정.
5. **웹 방화벽 (WAF) 사용:** SQL Injection 공격을 탐지하고 차단하는 웹 방화벽 도구를 활용.