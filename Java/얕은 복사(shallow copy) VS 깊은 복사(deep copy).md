# 얕은 복사(shallow copy)랑 깊은 복사(deep copy)
- 객체를 복사할 때 내부 데이터를 어떻게 다루느냐에 대한 개념

| 구분        | 설명                                           |
| --------- | -------------------------------------------- |
| **얕은 복사** | 객체만 복사하고, **내부 참조 객체는 주소만 복사**함 (같은 객체를 가리킴) |
| **깊은 복사** | 객체와 **내부 객체까지 전부 새로 복사**함 (완전히 독립된 복사본)      |

| 상황               | 복사 방식 |
| ---------------- | ----- |
| 성능 중요, 데이터 공유 OK | 얕은 복사 |
| 독립적인 객체 필요       | 깊은 복사 |


____

## 📌 예시 (Java 기준)
### 얕은 복사 예시
~~~ java
class Person {
    String name;
    Address address;
}

class Address {
    String city;
}

Person p1 = new Person("슬기", new Address("서울"));
Person p2 = p1; // 얕은 복사 (주소 공유)

p2.address.city = "부산";
System.out.println(p1.address.city); // 부산 😱

~~~

----

### 깊은 복사 예시
~~~ java
class Person {
    String name;
    Address address;

    public Person deepCopy() {
        return new Person(name, new Address(address.city));
    }
}

Person p1 = new Person("슬기", new Address("서울"));
Person p2 = p1.deepCopy();

p2.address.city = "부산";
System.out.println(p1.address.city); // 서울 😎

~~~