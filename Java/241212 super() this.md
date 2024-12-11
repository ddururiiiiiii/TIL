# super() VS this

## super()
- 자식 클래스에서 부모 클래스의 멤버(변수, 메소드 등)를 참조할 때 사용
- 부모 클래스 생성자 호출: 자식 클래스의 생성자에서 부모 클래스의 생성자를 호출할 때 사용합니다.
- 부모 클래스의 멤버와 자식 클래스의 멤버가 이름이 같을 때 명확하게 구분하기 위해 사용
- 부모 클래스의 생성자를 호출하여 초기화 작업을 수행
- 상속관계에서만 사용 가능하며 생성자에서만 사용.

## this
- 자신의 객체 참조: 현재 객체(즉, 자신)를 가리키는 참조 변수
- 자신의 클래스의 멤버 참조: 현재 클래스의 멤버(변수, 메소드 등)를 참조할 때 사용.
- 매개변수 이름과 멤버 변수 이름이 같을 때 멤버 변수를 명확하게 지정하기 위해 사용.
- 생성자 내에서 다른 생성자를 호출할 때 사용.

<br>

```java
class Parent {
    int x = 10;
}

class Child extends Parent {
    int x = 20;

    Child() {
        System.out.println("x = " + x); // 자식 클래스의 x 출력
        System.out.println("super.x = " + super.x); // 부모 클래스의 x 출력
    }
}
```