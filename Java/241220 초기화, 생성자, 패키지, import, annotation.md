## 초기화
- 변수 선언시 반드시 해줘야 하는 것으로 변수가 사용할 수 있는 환경이 되도록 하는 것을 뜻함.
```java
int age = 20; // 선언과 동시에 초기화
String name;
name = "홍길동"; // 나중에 초기화
```

## 생성자
- 자바클래스의 객체를 생성하기 위해 존재하는 것.
- 암묵적으로 클래스의 메소드 보다 상단에 위치
- 매개변수가 없는 기본생성자의 경우 따로 입력하지 않아도 컴파일시 자동 생성.
```java
class Person {
    String name;
    int age;

    // 생성자: 객체 생성 시 자동으로 호출되는 메소드
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

## 패키지
- 클래스를 그룹화 하는 단위
```java
// com.example.mypackage 패키지에 속하는 MyClass 클래스
package com.example.mypackage;

public class MyClass {
    // ...
}
```

## Import
- 다른 패키지에 접근하기 위한 예약어

## static Import
- 다른 클래스의 static변수를 참조할 때 사용한다.
```java
import static java.lang.Math.PI;
import static java.lang.System.out;

public class StaticImportExample {
    public static void main(String[] args) {
        // Math.PI 대신 PI만 사용 가능
        double radius = 10;
        double area = PI * radius * radius;
        
        // System.out.println() 대신 out.println() 사용 가능
        out.println("원의 넓이: " + area);
    }
}
```

## 어노테이션이션
- 컴파일러에게 정보를 알려주는 역할을 함
- Override : 부모에게 상속되어 재정의 되는 메서드라는 것을 명확히 알려줌
- Deprecated : 안쓰는 메소드니까 컴파일시에 경고해줘
- SuperessWarnings : 일부러 이렇게 한거니까 경고하지 않아도 돼
```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@interface MyAnnotation {
    String value() default "hello";
}

class MyClass {
    @MyAnnotation("world")
    public void myMethod() {
        // ...
    }
}
```