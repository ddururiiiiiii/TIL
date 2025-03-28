# 예외 (Exception)
- 프로그램이 실행되는 도중에 발생하는 예기치 못한 상황이나 오류.

## 예외의 종류
### 에러 (Error)
- 자바 프로그램 밖에서의 오류 (주로 [JVM](https://github.com/ddururiiiiiii/TIL/blob/main/Java/241218%20JVM.md)이나 시스템 레벨에서 발생)
- ex. 디스크 고장, OutOfMemoryError, StackOverflowError

### 체크 예외 (Checked exception)
- 컴파일 시점에 발생하는 에러 (IDE에서 실행조차 안됨)
- 존재하지 않는 파일의 이름을 입력하거나 실수로 클래스 이름을 잘못 적었을 때
- ex. IOException, SQLException, FileNotFoundException

### 언체크 예외 (Unchecked exception, Runtime exception)
- 런타임시점에 발생가능한 에러 (실행은 되지만 실행중에 에러 발생)
- 대부분 프로그래머의 실수로 인해 발생하기 때문에 컴파일러가 미리 예측하기 어려움.
- ex. NullPointerException, ArrayIndexOutOfBoundsException, IllegalArgumentException

```java
public class NullPointerExample {
    public static void main(String[] args) {
        String text = null;
        System.out.println(text.length()); // NullPointerException 발생
    }
}
```

<br>

### (+) 예외 처리를 하는 이유
- 예외가 발생 했을 때, 프로그램이 중단되지 않게끔 하기 위해. 예외 메시지를 통해서 원인을 파악하고 문제를 쉽게 해결하기 위해.

<br>

----

<br>

## 사용자 정의 예외 클래스
- 자바에서 기본 제공하는 예외 말고, 개발자가 직접 만드는 예외 클래스
- 기본 예외(NullPointerException, IllegalArgumentException)만으로는 에러 상황을 정확히 표현하기 어려울 때 사용

```java
// 사용자 정의 예외
class InvalidAgeException extends Exception {
    public InvalidAgeException(String message) {
        super(message); // 예외 메시지를 부모 클래스에 전달
    }
}
```

```java
public class CustomExceptionExample {
    // 나이를 확인하는 메서드
    public static void checkAge(int age) throws InvalidAgeException {
        if (age < 0 || age > 150) {
            throw new InvalidAgeException("나이는 0 이상 150 이하만 가능합니다."); // 예외 발생
        }
        System.out.println("유효한 나이입니다: " + age);
    }

    public static void main(String[] args) {
        try {
            checkAge(200); // 예외 발생
        } catch (InvalidAgeException e) {
            System.out.println("예외 발생: " + e.getMessage()); // 예외 메시지 출력
        }
    }
}

```

<br>

----

<br>

## 예외 처리 방법 3가지
### 1. 예외 처리 (Handling)
- try-catch로 예외를 직접 처리하는 것 (가장 일반적인 예외 처리 방법)
- 예외 발생 시 사용자에게 안내해야 할 때
```java
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("0으로 나눌 수 없습니다");
}
```

<br>

### 2. 예외 회피 (Propagation)
- 예외를 직접 처리하지 않고, throws로 호출한 쪽에 넘기는 것
- 책임을 다른 곳에 위임하고 싶을 때
```java
void readFile() throws IOException {
    FileReader fr = new FileReader("file.txt");
}
```
<br>

### 3. 예외 전환 (Translation)
- 발생한 예외를 다른 예외(내가 정의한 예외)로 바꿔서 던지는 것
- 사용자에게 더 명확한 메시지를 주고 싶을 때
```java
try {
    FileReader fr = new FileReader("file.txt");
} catch (FileNotFoundException e) {
    throw new RuntimeException("파일이 없습니다", e); // 예외 전환
}
```
<br>

----

<br>

## 예외 처리 성능 최적화
- 애초에 불필요한 예외를 작성하지 않기 (정상로직에 예외처리 하지 않음)
- 불필요한 try-catch 남발하지 않기
- 불필요한 예외 로그 줄이기
- finally 블록은 예외 여부와 상관없이 반드시 실행되기 때문에 무거운 작업을 피하기
