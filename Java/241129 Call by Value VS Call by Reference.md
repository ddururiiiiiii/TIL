# Call by Value VS Call by Reference


## 콜 바이 벨류(Call by Value)
- 값 자체를 '복사'해서 전달
- Java는 **항상 Call by Value** 방식을 사용

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

----

<br>

## 콜 바이 레퍼런스 (Call by Reference)
- 값을 복사하지 않고 **원래 변수의 주소(참조)의 원본을 전달**
- 원본을 직접 바꿀 수 있음.


<br>

----

<br>

### (+) Java는 Call by Value 일까? Call by Reference 일까? 
- 자바는 **항상 Call by Value 방식**만 사용해서, 기본타입(변수)의 경우에는 값을 바꿔도 원본은 바뀌지 않는데, 참조 타입(객체)일 경우, 객체의 주소(참조값)을 복사하기 때문에 값을 바꾸면 실제 객체의 내용도 바뀜.
