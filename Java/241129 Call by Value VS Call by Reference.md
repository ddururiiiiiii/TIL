# Call by Value VS Call by Reference


## 콜 바이 벨류(Call by Value)
- 값 자체를 '복사'해서 전달
- Java는 **항상 Call by Value** 방식을 사용 ('값'을 '복사'해서 전달)
- 객체의 참조값도 **값으로 복사**되기 때문에, **객체의 내용은 변경 가능**하지만 **참조 자체는 변경 불가능함.**

```java
void changeValue(int num) {
    num = 10; // 값만 바꿔도 원래 값은 그대로!
}

public static void main(String[] args) {
    int x = 5;
    changeValue(x);
    System.out.println(x); // 여전히 5
}

```

<br> 

### Java의 경우
- 자바는 **항상 Call by Value 방식**만 사용 하지만, 참조 타입(객체)일 때는 헷갈릴 수 있음.
```java
void modifyObject(StringBuilder sb) {
    sb.append(" World!"); // 참조된 객체 내용 변경
}

public static void main(String[] args) {
    StringBuilder sb = new StringBuilder("Hello");
    modifyObject(sb);
    System.out.println(sb); // 출력: Hello World!
}

```
```java
void changeReference(StringBuilder sb) {
    sb = new StringBuilder("New Object!"); // 참조 자체 변경
}

public static void main(String[] args) {
    StringBuilder sb = new StringBuilder("Hello");
    changeReference(sb);
    System.out.println(sb); // 출력: Hello (변경 안 됨)
}
```
-  메서드로 넘어온 참조값(주소)이 **복사된 값**일 뿐, 원래 참조값이 아니기 때문.

----

<br>

## 콜 바이 레퍼런스 (Call by Reference)
- 값을 복사하지 않고 **원래 변수의 주소(참조)의 원본을 전달**
- 마치 **원본 자체를 넘겨주는 것.** 원본을 직접 바꿀 수 있음.


----

<br>

### Java는 왜 Call by Reference가 아니고 Call by Value일까?
- **안정성** : 원래 변수의 참조가 멋대로 바뀌는 걸 막아주기 위해
- **디버깅이 쉬움** : 원래 변수가 갑자기 바뀌지 않으니, 오류를 찾기 쉬움.
- **단순한 메모리 모델** : Java의 메모리 모델은 복사된 값만 전달해서 참조관리가 단순하고 명확한데 가비지 컬렉션의 메모리 기능과 일맥상통함.