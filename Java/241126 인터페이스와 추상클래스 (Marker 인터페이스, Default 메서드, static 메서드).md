# 인터페이스와 추상클래스 (Marker 인터페이스, Default 메서드, static 메서드)

## 인터페이스(Interface)
- interface 키워드를 사용해 정의
- 구현은 하지 않고 선언(기능정의)만 하는 형태 → 이 클래스는 이런 기능을 반드시 제공한다를 명시
- 장점 : 선언과 구현을 분리해서 개발에만 집중 가능, 다중 구현이 가능.
- 단점 : 선언한 메소드들은 구현 클래스에서 반드시 구현해야 함 ([오버라이딩(Overriding)](https://github.com/ddururiiiiiii/TIL/blob/main/Java/241120%20Overloading%20VS%20Overriding.md))
- Default 메서드, Static메서드의 등장으로 추상클래스와의 경계가 모호.



<br>

----

<br>


## 추상클래스(Abstract)
- abstract 키워드를 사용해 정의
- 추상메소드, 일반 메소드를 모두 포함할 수 있는 클래스
- 공통 기능 + 규칙 정의이 모두 가능
- 장점 : 공통적인 기능은 추상 클래스에 구현하여 코드 중복 줄일 수 있음.
- 단점 : 다중상속 불가, 추상 메서드가 아닌 건 오버라이딩이 강제되지 않기 때문에 설계 일관성이 떨어질 수 있음.

```java
// 추상 클래스
abstract class Animal {
    // 일반 메서드: 공통 동작 정의
    public void eat() {
        System.out.println("먹고 있습니다.");
    }

    // 추상 메서드: 구체적 동작은 자식 클래스에서 정의
    public abstract void makeSound();
}
```

----

### (+) 어떨 때 인터페이스를 선택하고, 어떨 때 추상클래스를 선택해야 할까?
- 이런 기능을 꼭 구현해 → 인터페이스
- 이 기능은 기본 제공을 하고 나머지는 알아서 해 → 추상클래스


<br>

----

<br>


## 마커 인터페이스(Marker Interface)
- 아무런 메서드도 없는 인터페이스
- 사용 하는 이유
  - 물건에 붙이는 라벨과 같이 해당 클래스가 특정 기능을 수행할 수 있다는 것을 명시적으로 알려줌.
    - ex. Active 마커인터페이스를 구현하면 현재 클래스가 '활성화' 상태라는 것을 명시적으로 표기.
  - 프레임워크, JVM에게 추가적인 졍보를 제공해서 특별한 처리를 하도록 유도. 
    - ex. Serializable(직렬화 가능) / Cloneable(복제 가능) / Remote(원격 호출 가능) / RandomAccess(랜덤 접근 최적화)

#### 코드 예시

```java
interface Serializable {}

class Person implements Serializable {}
```

<br>

----

<br>

## 디폴트 메서드 (Default Method)
- Java8부터 도입된 기능, default 키워드 사용.
- 인터페이스에서도 메서드를 가질 수 있게 만든 기능
- 기존의 인터페이스 변경없이 기능을 추가하게끔 하려고 만든 기능.
```java
interface Vehicle {
    // 일반 메서드 (추상 메서드)
    void start();

    // 디폴트 메서드
    default void stop() {
        System.out.println("Vehicle is stopping...");
    }
}
```

### (+) 디폴드 메서드를 잘 안쓰는 이유
- 인터페이스는 다중 구현이 가능한데 다중 구현을 하게되면 여러 인터페이스에서 같은 이름의 디폴드 메서드가 있을 경우 충돌할 수 있음.
- 인터페이스는 원래 '기능정의'만 하는 것인데 구현도 가능해지면서 객체지향에서 모호해짐.

<br>

----

<br>

## Static 메서드
- Java8 부터 도입된 기능.
- 객체 생성 없이 클래스 이름으로 바로 호출할 수 있는 메서드
- 유틸리티 클래스 만들때, 객체와 무관한 기능일 때
- ex. Math.random(), Integer.parseInt()

```java
interface MyInterface {
    static void staticMethod() {
        System.out.println("스태틱 메소드");
    }
}
```
