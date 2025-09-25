
# DTO 
- DTO는 계층 간 (예: 컨트롤러 ↔ 서비스 ↔ DB) 데이터를 전달하기 위한 객체
- 로직 없이 필드만 담고 있는 순수한 데이터 전달용 객체
- 예)
~~~ Java
public class UserDto {
    private String name;
    private int age;
    // 생성자, getter, setter, toString, equals, hashCode 등 다 만들어야 함...
}
~~~

<br>

# Record
~~~ Java
public record UserDto(String name, int age) {}
~~~
- 위와 같이 만들어 주면 필드 선언, 생성자, getter (name(), age()), toString(), equals(), hashCode() 모두 다 자동으로 만들어줌.
- 불변 객체(immutable)

<br>

## 왜 record를 DTO로 쓰나요?
1. 코드가 짧고 간결함
- boilerplate 코드 없음 → 클래스 + getter + 생성자 안 만들어도 됨
- 읽기도 쉽고 유지보수도 쉬움

2. 불변성(immutable) 보장
- DTO는 원래 값만 전달하고 끝나야 함 → setter 없이 값 고정되는 record가 적합
- 안정성/스레드 안정성도 확보

3. 의도가 명확해짐
- "나는 단순히 데이터를 옮기기 위한 용도다"를 코드 자체로 표현
- 불변 객체니까 실수로 변경할 일도 없음

4. 직렬화, Jackson 매핑도 잘 됨
- Spring Boot + Jackson 환경에서는 record도 자동으로 JSON ↔ 객체 변환 잘 됨
- 실제 컨트롤러에서 요청/응답 DTO로 사용 가능

<br>

(+) 주의
- record는 불변이라 필드 수정 불가
- 로직을 추가할 수 없기 때문에 비즈니스 로직 있는 객체엔 부적합
- JPA 엔티티로는 사용 ❌ (디폴트 생성자 없음, 프록시 문제 등)

