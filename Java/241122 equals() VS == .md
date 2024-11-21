# equals()VS == equals()
- equals() 메서드와 == 연산자 모두 객체를 비교하는데 사용하지만 비교하는 대상이 다름. 

<br>

## equals() 메서드
- equals() 메서드는 두 객체의 '값'을 비교.
- 즉, 두 객체가 같은 값을 가지는지 확인.

## == 연산자
- == 연산자는 두 객체의 '주소(참조)'값을 비교.
- 즉, 두 변수가 동일한 객체를 가리키는지 확인.
- 기본자료형(int, double 등)의 경우 값을 직접 비교하지만, 참조형의 경우 객체의 주소를 비교함.

### 코드 예시
```java
String str1 = "hello";
String str2 = new String("hello");

System.out.println(str1 == str2); // false (다른 객체를 참조)
System.out.println(str1.equals(str2)); // true (문자열 내용이 같음)
```
### 결과값
- tr1 == str2 -> false
- str1.equals(str2) -> true

### 결론
- 기본자료형의 값이 같은지 확인 할 때 -> ==
- 참조자료형에서 두 객체가 완전히 같은 객체인지(같은 메모리 주소를 가르키는지) 확인할 때 -> ==
- 참조자료형에서 두 객체가 '같은 값'인지 확인 할 때 -> equals()