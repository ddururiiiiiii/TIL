# Comparable VS Comparator

## **Comparable 인터페이스**
- 자기 자신을 다른 객체와 비교할 수 있는 기준을 제공하는 인터페이스
- 객체 클래스 내부에서 정의
- 하나의 클래스에 대해 하나의 정렬기준만 정의

### 주요 메서드:
- `compareTo(T o)`
    - 현재 객체(`this`)와 비교 대상 객체(`o`)를 비교한 결과를 정수로 반환
    - return값의 의미
        - 양수: this 객체가 더 큰 값
        - 음수: 매개변수 객체가 더 큰 값
        - 0: 두 객체가 같은 값

```java
class Person implements Comparable<Person> {
    private String name;
    private int age;

    // ... 생략

    @Override
    public int compareTo(Person other) {
        return this.age - other.age; // 나이순으로 정렬
    }
}
```
- `Collections.sort` 메서드가 **Comparable 인터페이스를 구현한 객체**를 자동으로 인식하여 `compareTo` 메서드를 호출하기 때문.


## **Comparator 인터페이스**
- 객체 간의 비교 기준을 외부에서 정의하는 인터페이스
- 객체 클래스 외부에서 정의
- 하나의 클래스에 대해 여러 개의 Comparator을 만들어 다양한 정렬 기준을 정의.
- Comparable 인터페이스보다 복잡한 정렬 로직 구현 가능해 더 유연하게 객체를 비교할 수 있음.

### 주요 메서드:

- `icompare(T o1, T o2)`
    - 두 객체(`o1`과 `o2`)를 비교하여 정수를 반환
    - **결과값의 의미:**
        - `음수`: `o1`이 `o2`보다 작다
        - `0`: `o1`과 `o2`가 같다.
        - `양수`: `o1`이 `o2`보다 크다.

```java
class PersonNameComparator implements Comparator<Person> {
    @Override
    public int compare(Person p1, Person p2) {
        return p1.getName().compareTo(p2.getName()); // 이름순으로 정렬
    }
}
```


## **Comparable**과 **Comparator** 인터페이스 의 return 값이 양수, 음수, 0으로 나오는 이유
- 정렬 알고리즘는 내부적으로 두 값을 비교하여 교환할지 결정하는 방식
- 양수, 음수, 0으로 반환하면 비교 결과를 바로 사용할 수 있어서 처리속도가 빠름.
- 정렬 알고리즘 내부에서 굳이 별도 변환이 필요 없이, 반환값의 부호로만 조건을 확인할 수 있어 효율적으로 처리.