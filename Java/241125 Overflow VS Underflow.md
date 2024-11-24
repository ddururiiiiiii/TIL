# Overflow, Underflow
- 데이터 표현의 한계로 인해 발생하는 오류
- 특정 데이터 타입이 표현할 수 있는 범위를 넘어서는 값이 할당 될 때 발생.
- 발생 시, 계산이 정확하지 않을 수 있고 프로그램 오류가 발생함.
- 시계가 12시 다음이 13시가 아니라 1시로 넘어가는 원리.

<br>

## 오버플로우 (Overflow)
- 어떤 데이터 타입이 표현할 수 있는 최댓값보다 더 큰 값이 할당될 때 발생하는 오류
- ex. 8비트 정수형 변수는 -128 ~ 127까지의 값을 표현할 수 있음. 만약 127에 1을 더하면 128이 되어야 하지만, 8비트 정수형의 최댓값을 초과하기 때문에 -128로 돌아가는 현상이 발생 -> 오버플로우 발생

### 코드 예시

```java
public class OverflowExample {
    public static void main(String[] args) {
        byte num = 127; // byte형의 최댓값
        System.out.println(num); // 127 출력

        num++; // 127에 1을 더하면 128이 되어야 하지만, byte형의 범위를 넘어서므로 -128이 된다.
        System.out.println(num); // -128 출력
    }
}
```
<br>

## 언더플로우 (Underflow)
- 어떤 데이터 타입이 표현할 수 있는 최솟값보다 더 작은 값이 할당될 때 발생하는 오류
- ex. 부동소수점 수에서 0에 매우 가까운 양수에 0을 나누면 0이 되어야 하지만, 실제로는 매우 작은 양수가 될 수 있음 -> 언더플로우 발생

### 코드 예시

```java
public class UnderflowExample {
    public static void main(String[] args) {
        float num = 0.000000001f; // 매우 작은 양수
        num /= 1000000000; // num을 10억으로 나눔
        System.out.println(num); // 0.0 출력될 수 있음 (실제로는 매우 작은 양수일 수 있음)
    }
}
```

### 예방 및 해결 방법
- 애초에 데이터의 크기와 범위에 맞는 적절한 데이터 타입을 선택하기.
- BigInteger(임의의 크기의 정수)와 BigDecimal(임의의 정밀도의 소수)을 사용하기
  - 이유
    - 기본형의 경우는 제한된 크기의 메모리를 사용 -> 매우 큰수는 정확하게 표현할 수 없음
    - 부동소수점 연산의 경우 근사값을 사용하기 때문에 정밀도가 떨어짐

### 코드 예시

```java
import java.math.BigInteger;
import java.math.BigDecimal;

public class BigNumbers {
    public static void main(String[] args) {
        // 매우 큰 정수 연산
        BigInteger bigInt1 = new BigInteger("12345678901234567890");
        BigInteger bigInt2 = new BigInteger("98765432109876543210");
        BigInteger result = bigInt1.multiply(bigInt2);
        System.out.println(result);

        // 정밀한 소수 연산
        BigDecimal bigDecimal1 = new BigDecimal("3.141592653589793");
        BigDecimal bigDecimal2 = new BigDecimal("2.718281828459045");
        BigDecimal result2 = bigDecimal1.divide(bigDecimal2, 10, BigDecimal.ROUND_HALF_UP); // 소수점 아래 10자리까지 반올림
        System.out.println(result2);
    }
}
```