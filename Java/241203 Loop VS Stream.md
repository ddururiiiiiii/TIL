# Loop VS Stream
- 루프와 스트림에 대해 정리


## 루프 (Loop)
- 코드 블록을 반복적으로 실행하는 구조 (for, while문)
- 명령형 프로그래밍 방식 → 각 요소를 직접 접근하여 처리
- 장점
    - **직관적이고 명확**: 코드를 처음 접하는 사람도 이해하기 쉬움.
    - **디버깅 용이**: 중간 상태를 직접 추적하고 확인 가능.
    - **복잡한 로직 처리**: 조건문과 반복문이 많은 경우 유리.
- 단점
    - **장황한 코드**: 처리 단계가 많아질수록 코드가 길어지고 가독성이 떨어짐.
    - **병렬 처리 어렵다** : 병렬 처리 구현이 복잡하고 오류 가능성 증가.

```java
import java.util.Arrays;
import java.util.List;

public class LoopExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);
        int sum = 0;

        // 명령형 루프
        for (int num : numbers) {
            if (num % 2 == 0) { // 짝수 필터링
                sum += num; // 합산
            }
        }

        System.out.println("Sum of even numbers (Loop): " + sum); // 출력: 12
    }
}

```

----


# 스트림 (Stream)
- Java8에서 도입된 개념
- 스트림 : 데이터의 흐름을 추상화한 것
- 선언형 프로그래밍 방식 → 데이터의 변환, 처리 과정을 함수 연결 형태로 표현
- 장점
    - **간결한 코드**: 선언형 스타일로 작업을 체인 형태로 작성 가능.
    - **가독성 높음**: "데이터가 어떻게 처리되는지"를 명확히 표현.
    - **병렬 처리 간단**: `.parallelStream()`으로 쉽게 병렬 처리.
- 단점
    1. **디버깅 어려움**: 중간 단계에서 상태를 추적하기 어려움.
    2. **학습 곡선**: 스트림 API를 처음 배우는 데 시간이 필요.
    3. **단순 반복 작업에는 과잉 설계**: 간단한 작업에도 스트림을 사용하면 과도한 코드로 느껴질 수 있음.

```java
import java.util.Arrays;
import java.util.List;

public class StreamExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);

        // 선언형 스트림
        int sum = numbers.stream()
                         .filter(num -> num % 2 == 0) // 짝수 필터링
                         .mapToInt(Integer::intValue) // int로 변환
                         .sum(); // 합산

        System.out.println("Sum of even numbers (Stream): " + sum); // 출력: 12
    }
}

```

## 루프(Loop) VS 스트림(Steram)
- 어떤 것이 빠르다고 특정할 수 없음. 상황에 따라 다름.
- 일반적으로 작은 데이터 셋에서는 루프가 빠르고 큰 데이터 셋에서는 스트림이 빠를 수도 있음.
    - 루프는 오래 전 부터 쓰던 거라 내부 최적화가 잘되어있고 단순 인덱스 기반으로 돌기 때문에 빠르고 오버헤드도 없음
    - 스트림은 오버헤드 발생가능성있음 (JVM에서 처리하는 작업도 있고, 원시타입이 아닌 래퍼타입이라 박싱/언박싱 과정에서 오버헤드 발생할 수 있음)
    - 큰 데이터 셋의 경우, 스트림도 중간연산이 많거나 복잡하면 오히려 느릴 수도 있음.
- 루프의 경우 countine, break처럼 제어할 수 있기 때문에 디테일한 조정이 필요할 때는 루프가 적절할 수 있음.

## 그래도 스트림(Stream)을 사용하는 이유?
- 가독성과 편의성에 압도적으로 뛰어나서.
- 데이터의 양이 많거나 참조타입을 반복하고 싶을 때는 스트림을 사용해도 성능 차이가 그렇게 크지 않음.