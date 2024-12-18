# 리플렉션 (Reflection)
- 반사, 반영이라는 뜻 → 프로그램이 자기 자신을 되돌아보고 자신의 구조와 동작에 대해 알아내는 기능.
- 장점
    - 클래스에 대한 정보(생성자, 메소드, 필드 등)를 아주 자세히 알아낼 수 있음.
    - 접근제어자와 무관하게 클래스나 필스, 메소드 호출이 가능함.
    - 동적으로 객체를 생성.
- 단점
    - 일반적인 메소드 호출은 컴파일 시점에 클래스를 사용 → 리플렉션은 런타임에 클래스를 분석해서 속도가 느림.
    - 타입체크가 컴파일 타임에 불가능함.
    - 접근 제한을 무시할 수 있어서 잘못 사용하면 보안, 안정성 문제가 발생할 수 있음 → 정말 필요한 곳에서만 리플랙션을 한정적으로 사용해야 함.
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