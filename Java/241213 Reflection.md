# 리플렉션 (Reflection)
- 반사, 반영이라는 뜻
- 런타임(프로그램 실행 중)에 클래스, 메서드, 필드 등의 정보를 조사하고, 필요하면 수정하거나 호출할 수 있는 기능을 제공.
- 장점
  - 동적 동작 지원 : - 컴파일 시점에 알 수 없는 클래스나 메서드를 런타임에 사용할 수 있습니다.
  - 프레임워크 및 라이브러리 제작에 필수적 : 스프링(Spring) 같은 프레임워크는 리플렉션을 사용해 의존성 주입, 애노테이션 처리 등을 수행
- 단점
  - 성능 저하 : 리플렉션은 일반적인 메서드 호출보다 느림.
  - 캡슐화 위반 가능성 : private 필드나 메서드에 접근할 수 있어 객체의 안전성을 해칠 수 있음.
  - 복잡성 증가 : 리플렉션을 잘못 사용하면 디버깅이 어렵고 코드가 읽기 힘들어짐.

- 주로 사용하는 경우 (컴파일 시점이 아니라 런타임 시점에 무언가를 동적으로 생성, 수정해야 할 때)
    - 프레임워크, 라이브러리 개발 : 프레임 워크는 사용자가 작성한 코드를 동적으로 처리해야 하는 경우가 많아서 리플랙션이 필수적임 (ex. 스프링의 @Autowired 어노테이션이 붙은 필드를 찾아서 객체를 자동주입해줌)
    - 디버깅 및 테스트 : private 메서드나 필드에 접근해야 할 때 리플랙션을 사용하면 내부 로직을 테스트, 디버깅 가능. (ex. J unit, @Test)
    - JSON 문자열을 객체로 변환 (GSon라이브러리는 Json을 자바 객체로 변환하는데 내부 동작시에 리플렉션이 사용됨.)
```java
import java.lang.reflect.Method;

class Person {
    public void sayHello() {
        System.out.println("Hello");
    }
}

public class ReflectionExample {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, SecurityExcept 1 ion {
        // Person 클래스를 동적으로 로딩
        Class<?> personClass = Class.forName("Person");

        // Person 객체 생성
        Object personObj = personClass.newInstance();

        // sayHello() 메소드를 찾아 호출
        Method method = personClass.getMethod("sayHello");
        method.invoke(personObj);
    }
}
```

## 제네릭과 리플렉션의 관계
- 제네릭은 컴파일러가 타입을 확인하는 데만 사용되고 런타임시점에서는 해당 정보가 제거됨 → 런타임에서 제네릭 정보를 확인하거나 다루기 위해 리플렉션이 필요!