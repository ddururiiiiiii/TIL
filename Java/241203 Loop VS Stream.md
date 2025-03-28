# Loop VS Stream
- 루프와 스트림에 대해 정리


## 루프(Loop)
- 코드 블록을 반복적으로 실행하는 구조 (for, while문)
- 명령형 프로그래밍 방식(어떻게 처리할지 하나하나 명시)
- 장점 : 간단한 로직에서는 가독성이 좋고 복잡한 로직 처리에 유리함. 디버깅이 쉽고 직관적.
- 단점 : 병렬처리 시 직접 쓰레드를 작성해서 복잡함.
```java
List<String> names = Arrays.asList("milk", "love", "jim");
List<String> result = new ArrayList<>();
for (String name : names) {
    if (name.startsWith("l")) {
        result.add(name.toUpperCase());
    }
}
```

### 언제 루프를 쓰면 좋을까?
- 중간에 break/continue 써야 할 때
- index 접근이 필요할 때 (i 값 등)
- 중간 상태 디버깅이 중요할 때

<br>

----

<br>


# 스트림(Stream)
- Java8에서 도입된 개념 (***스트림 : 데이터의 흐름을 추상화한 것)
- 선언형 프로그래밍 방식(데이터의 변환, 처리 과정을 함수 연결 형태로 표현)
- 장점 : 필터링, 매핑 등 여러 처리에 가독성 좋고 병렬 처리가 쉬움.
- 단점 : 중간 처리를 보기 어려움

```java
List<String> result = names.stream()
    .filter(name -> name.startsWith("l"))
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```

### 언제 루프를 쓰면 좋을까?
- 가독성과 유지보수가 중요할 때
- 병렬 처리 (parallelStream)가 필요한 경우
- 코드를 함수형 스타일로 간결하게 쓰고 싶을 때

<br>

----

<br>

## 루프(Loop) vs 스트림(Stream)
- 단순 반복 (작은 데이터) → 루프가 빠름 (스트림은 약간 느림)
- 복잡한 로직 / 필터링	→ 거의 비슷하거나 스트림이 더 명확함
- 대량 데이터 + 병렬처리 → parallelStream()이 유리할 수 있음
*** 성능 차이가 생각보다 크지 않고, 가독성과 편리성이 스트림이 압도적으로 좋음으로 스트림을 많이 쓰는 추세