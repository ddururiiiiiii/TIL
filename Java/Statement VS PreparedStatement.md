# Statement VS PreparedStatement


## Statement
~~~ java
Statement stmt = conn.createStatement();
String sql = "SELECT * FROM users WHERE name = '" + name + "'";
ResultSet rs = stmt.executeQuery(sql);
- 문자열을 직접 이어붙임
- 사용자 입력이 그대로 들어감 → 위험함! 🔥

##  PreparedStatement
~~~ java
PreparedStatement pstmt = conn.prepareStatement(
  "SELECT * FROM users WHERE name = ?"
);
pstmt.setString(1, name);
ResultSet rs = pstmt.executeQuery();
~~~
- ? 자리에 안전하게 값만 채워넣음
- SQL Injection 걱정 없음!
- 보안 + 성능면에서 훨씬 뛰어남.

## 차이점
| 구분  | Statement             | PreparedStatement         |
| --- | --------------------- | ------------------------- |
| 정의  | 그냥 SQL 문 실행           | **미리 컴파일된** SQL 문 실행      |
| 사용법 | 문자열로 SQL 작성           | `?`로 값 자리를 미리 지정함         |
| 보안  | SQL Injection 위험 있음 ❌ | SQL Injection 방어됨 ✅       |
| 성능  | 반복 실행 시 느림 ❌          | 반복 실행 시 빠름 ✅ (컴파일 한 번만 함) |
