# Overloading, Overriding
- 비슷한 이름을 가지고 있지만 엄연히 다른 개념이다. 

<br>


## 오버로딩 (Overloading)
- 같은 클래스 내에서 메소드를 다양하게 정의하는 것
- 조건
  - 메소드 이름이 같아야 함.
  - 매개 변수의 개수, 타입, 순서가 달라야 함. 
  - 리턴 타입은 달라도 됨.
  - 사용하는 이유 : 동일한 작업을 수행하지만, 입력 형태가 다른 경우 사용
  - 이점 : 코드 재사용성, 가독성 향상

### 코드 예시

```java
public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
    
    public double add(double a, double b) {
        return a + b;
    }
}
```
<br>

## 오버라이딩 (Overriding)
- 상속 관계에서 메소드를 재정의하는 것
- 조건
  - 메소드 이름, 매개변수, 리턴타입이 부모 클래스의 메소드와 완전히 같아야 함.
  - 접근 제한자는 부모 클래스의 메소드보다 더 넓은 범위로 변경 가능함.
  - 이점 : 다형성, 코드의 재사용성, 가독성 향상.

### 코드 예시

```java
  class Animal {
    public void sound() {
        System.out.println("동물의 소리");
    }
}

class Dog extends Animal {
    @Override
    public void sound() {
        System.out.println("멍멍");
    }
}
```


<br>


|구분 | 오버로딩 | 오버라이딩|
|---|---|---|
정의 | 같은 이름의 메소드 여러 개 정의 | 부모 클래스 메소드 재정의|
조건 | 매개변수 다름 | 메소드 시그니처 동일|
목적 | 코드 재사용성, 가독성 향상 | 다형성 구현|
결정시점 | 컴파일 시정 | 실행 시점|