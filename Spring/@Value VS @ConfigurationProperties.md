# @Value
- application.yml이나 .properties에 있는 값을 필드에 주입해주는 어노테이션.

~~~ java
@Value("${myapp.name}")
private String appName;
~~~

## @Value 사용 시 주의할 점
| 문제                | 설명                                                     |
| ----------------- | ------------------------------------------------------ |
| 💥 타입 변환 오류 발생 가능 | 기본형(String → int 등) 자동 변환이 잘 안 되면 애플리케이션이 **실행 중에 뻗음** |
| 🧪 테스트 어려움        | 단일 값 하나씩 주입이라 **테스트할 때 mock/stub 불편**                  |
| ✍️ 반복 코드 많아짐      | 여러 개 주입하려면 **@Value 여러 번 써야 해서 귀찮고 지저분**함              |
| 💣 리팩토링에 취약       | Key 이름이 문자열이라 IDE에서 추적이 어려움 (`"myapp.name"` 이런 거)      |
| 🤯 복잡한 구조(X)      | Map, List, Nested 구조 등 복잡한 값 주입은 힘듦                    |

<br>

----

<br>

# @ConfigurationProperties
- 설정 파일의 **"묶음 단위"**를 통째로 주입하는 방식
~~~ yml
myapp:
  name: bookclub
  version: 1.0
~~~
~~~ java
@ConfigurationProperties(prefix = "myapp")
@Component
public class MyAppProperties {
    private String name;
    private String version;
}

~~~

### 차이점

| 항목           | `@Value`          | `@ConfigurationProperties` |
| ------------ | ----------------- | -------------------------- |
| 📌 용도        | 단일 값 주입           | 여러 값을 묶어서 주입               |
| 🧹 코드 깔끔함    | ❌ (여러 개면 반복됨)     | ✅ (객체로 깔끔하게)               |
| 🧪 테스트 편의성   | ❌                 | ✅                          |
| 🔧 복잡한 구조 지원 | ❌ (List, Map 안 됨) | ✅ 가능함                      |
| 🧠 타입 변환     | 제한적               | 스프링이 자동으로 처리 잘해줌           |
| ✍️ Key 관리    | 문자열로 직접 입력        | 자동 매핑이라 안전함                |
| ⚙️ 바인딩 시점    | 런타임 (실행 중)        | 애플리케이션 시작 시점 (컴파일 타임에 가까움) |
- @Value는 간단하지만 지저분하고 위험할 수 있, 설정을 다루려면 무조건 @ConfigurationProperties를 쓰는 것이 좋다.