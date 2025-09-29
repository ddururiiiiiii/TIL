# String() VS String.valueOf()
- 자바에서 Object 타입인 value를 String으로 타입 캐스팅하는 것과 String.valueOf()를 사용하는 것의 차이점 알기

## 차이점
- (String) value는 진짜 String인 경우에만 캐스팅 가능
- String.valueOf(value)는 null이어도 안전하게 문자열로 변환해줌
- 값이 정말 String인지 확실할 때만 (String) 사용, 값이 null일 수도 있고 타입이 뭔지 모를 땐 String.valueOf() 써야 안전함

## 예시
~~~ java
Object value1 = "hello";
Object value2 = null;
Object value3 = 123;
~~~

1. (String) value
~~~ java
String s1 = (String) value1; // ✅ "hello"
String s2 = (String) value2; // ❌ NullPointerException ❗
String s3 = (String) value3; // ❌ ClassCastException ❗
~~~
- value가 진짜 String 타입이 아니면 캐스팅 실패
- null은 캐스팅 가능하지만 → 이후 사용 시 NPE 날 가능성 높음
- 123 같은 숫자를 강제로 (String) 하면 바로 ClassCastException 터짐


2. String.valueOf(value)
~~~ java
String s1 = String.valueOf(value1); // ✅ "hello"
String s2 = String.valueOf(value2); // ✅ "null"
String s3 = String.valueOf(value3); // ✅ "123"
~~~
- null이어도 "null"이라는 문자열로 안전하게 변환됨
- 어떤 객체든 .toString() 결과를 문자열로 만들어줌
- 숫자, 객체, null 전부 OK
- 