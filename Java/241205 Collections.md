# 컬렉션(Collections)
- 여러 개의 데이터를 효율적으로 다루기 위한 도구 상자
- 다양한 데이터를 효율적으로 관리, 처리 할 수 있음

## 컬렉션의 종류와 용도
- **List :** 순서가 유지되고 중복을 허용하는 컬렉션
    - **ArrayList**
        - 가장 일반적으로 사용 됨.
        - 장점 : 임의 접근, 검색이 빠름 : 인덱스를 이용하기 때문에
        - 단점 : 중간에 요소 추가/삭제할 때 시간이 오래 걸릴 수 있음. → 나머지 요소들을 이동해야 해서.
        - 임의 접근 빈번, 요소 추가/삭제 적은 경우
    - **LinkedList**
        - 노드(Node) 라는 개념을 이용하여 데이터를 연결하는 방식
        - 장점 : 중간에 요소 추가/삭제 할 때 빠름 → 연결된 노드만 변경하면 되서
        - 단점 : 임의 접근이 느림. → 접근하기 위해서는 처음부터 순차적으로 검색해야함.
        - 중간에 요소를 자주 추가/삭제 해야 하는 경우, 순차적인 접근이 주를 이루는 경우
        - 
    - **벡터(Vector)**
        - 동적으로 크기가 변경되는 배열을 제공하는 List 클래스
        - ArrayList와 유사하지만, 동기화 기능을 지원하여 멀티 스레드 환경에서 안전하게 사용할 수 있음. → 내부적으로 synchronized 키워드를 사용하고, 이로 인해 성능이 떨어질 수 있음.
        - 장점
            - 크기를 자동으로 늘릴 수 있어서 초기에 크기를 알 수 없을 때 유용.
            - 멀티 스레드 환경에서 안전하게 데이터를 관리할 수 있음.
        - 단점
            - 모든 작업에서 동기화를 하기 때문에 오히려 단일 스레드 환경에서 느림.
            - 요즘에는 잘 사용하지 않음.
        - 대체제
            - **단일 스레드 환경**에서는 **ArrayList**를 사용해 성능을 극대화 가능.
            - **멀티스레드 환경**에서는 **CopyOnWriteArrayList**나 `Collections.synchronizedList()`로 ArrayList를 동기화하거나,혹은 더 나은 대안 자료구조(예: ConcurrentHashMap, BlockingQueue 등)를 사용.

#### ArrayList 예시
```Java
import java.util.ArrayList;
import java.util.List;

public class ListExample {
    public static void main(String[] args) {
        List<String> fruits = new ArrayList<>();
        fruits.add("apple");
        fruits.add("banana");
        fruits.add("apple"); // 중복 허용
        System.out.println(fruits); // [apple, banana, apple]
    }
}
```


#### Vector 예시
```java
import java.util.Vector;

public class VectorExample {
    public static void main(String[] args) {
        Vector<String> vector = new Vector<>();
        vector.add("Apple");
        vector.add("Banana");
        vector.add("Cherry");

        System.out.println(vector); // 출력: [Apple, Banana, Cherry]
    }
}
```

----


- **스택(Stack)** : **Vector**를 기반으로 만들어진 **LIFO (Last In, First Out)** 구조의 자료구조 (마지막에 넣은 데이터를 먼저 꺼냄)
  - 장점
      - 후입선출 작업이 필요할 때 유용
      - **재귀적인 문제 해결**, **Undo/Redo 기능**, **탐색 알고리즘**(DFS) 등에 유용
  - 단점
      - 내부적으로 동기화를 하기 때문에 단일 스레드 환경에서는 성능이 떨어짐.
      - 스레드 안전하게 동작하지만, **Deque**(예: `ArrayDeque`)가 더 효율적.

```java
import java.util.Stack;

public class StackExample {
    public static void main(String[] args) {
        Stack<Integer> stack = new Stack<>();
        stack.push(10); // 데이터 추가
        stack.push(20);
        stack.push(30);

        System.out.println(stack.pop()); // 출력: 30 (가장 마지막에 추가된 데이터)
        System.out.println(stack.peek()); // 출력: 20 (맨 위 데이터 확인)
    }
}
```

<br>

----


- **Set :** 중복을 허용하지 않는 컬렉션
    - **HashSet**
        - 장점
            - 해시 테이블을 기반으로 하여 검색, 삽입, 삭제 속도가 매우 빠름
            - 하나의 값만 null 허용
        - 단점 : 순서를 보장하지 않음
        - 순서가 상관없고 중복을 허용하지 않고 빠른 검색이 필요한 경우 → 객체의 중복 여부를 확인, 캐시 구현
    - **TreeSet**
        - 장점 : 순서 보장. 이진 트리 구조를 기반으로 하여 데이터를 오름차순으로 정렬
        - 단점 : 검색, 삽입, 삭제 속도가 상대적으로 느림
        - Null 허용하지 않음
        - 정렬된 순서를 유지해야 하거나, 범위 검색이 필요한 경우에 사용 → 단어 사전, 점수 순위 관리

```java
import java.util.HashSet;
import java.util.Set;

public class SetExample {
    public static void main(String[] args) {
        Set<String> fruits = new HashSet<>();
        fruits.add("apple");
        fruits.add("banana");
        fruits.add("apple"); // 중복 무시
        System.out.println(fruits); // [apple, banana] (순서가 달라질 수 있음)
    }
}
```

----

- **Map**
    - 키와 값의 쌍으로 이루어진 컬렉션.
    - 키는 중복될 수 없지만 값은 중복될 수 있음.
    - **키-값 쌍으로 데이터를 저장해야 하는 경우**
    - **HashMap**
        - 장점
            - 해시 테이블을 기반으로 하여 검색, 삽입, 삭제 속도가 매우 빠름
            - 하나의 값만 null 허용
        - 단점 : 순서를 보장하지 않음
        - 키와 값 모두 null 허용
    - **TreeMap**
        - 장점 : 순서 보장. 이진 트리 구조를 기반으로 하여 데이터를 오름차순으로 정렬
        - 단점 : 검색, 삽입, 삭제 속도가 상대적으로 느림
        - 키와 값 모두 Null 허용하지 않음

```java
import java.util.HashMap;
import java.util.Map;

public class MapExample {
    public static void main(String[] args) {
        Map<String, Integer> ages = new HashMap<>();
        ages.put("Alice", 30);
        ages.put("Bob", 25);
        System.out.println(ages.get("Alice")); // 30
    }
}
```

----

- **큐(Queue)**
  - 자바에서는 **`Queue` 인터페이스**를 사용 (주요 구현체로 `LinkedList`와 `PriorityQueue` 등)
  - 선입선출(FIFO) 방식의 자료구조 (즉, 먼저 넣은 데이터를 먼저 꺼냄)
  - 장점 : 데이터가 순차적으로 처리될 때 적합
  - 단점 : 특정한 데이터 검색/수정에는 적합하지 않음.
  - **작업 큐**, **이벤트 처리 시스템**, **프로세스 관리** 등에 활용

  -  **큐(Queue) 종류**
     1. **Simple Queue** : FIFO 구조로 동작하는 일반적인 큐.
     2. **PriorityQueue** : 우선순위에 따라 데이터가 정렬되며, 우선순위가 높은 데이터가 먼저 나옴.
     3. **Deque** (Double-ended Queue) : 양쪽에서 데이터를 추가/삭제할 수 있는 구조.

```java
import java.util.LinkedList;
import java.util.Queue;

public class QueueExample {
    public static void main(String[] args) {
        Queue<String> queue = new LinkedList<>();
        queue.add("Task1");
        queue.add("Task2");
        queue.add("Task3");

        System.out.println(queue.poll()); // 출력: Task1 (가장 먼저 추가된 데이터)
        System.out.println(queue.peek()); // 출력: Task2 (다음 처리할 데이터 확인)
    }
}

```

----

- **디큐(Deque)**
  - 큐와 스택의 기능을 모두 가지고 있는 자료구조. (양쪽 끝에서 데이터를 넣고 뺄 수 있음.)
  - 양쪽 끝에서 데이터를 자주 추가하거나 삭제해야 할 때 사용됨.

  - **ArrayDeque**
      - 배열 기반의 Deque (가장 자주 사용됨)
      - 내부적으로 **동적 배열**로 구현되어 있어 빠르고 효율적
      - 크기 제한이 없으며, 필요에 따라 크기를 자동으로 확장
      - 멀티스레드 환경에서는 외부에서 동기화를 적용
      - Stack 대체제로 유용 (성능이 `Stack`보다 더 좋음).
      - Queue보다 더 유연하고, 특정 작업에서 `LinkedList`보다 효율적.
  - **LinkedList**: 연결 리스트 기반의 Deque
    - LinkedList는 노드(node)라는 연결된 구조로 되어 있어, 데이터의 삽입과 삭제가 양쪽 끝에서 효율적으로 이루어질 수 있음. 이러한 특징 때문에 List의 순차적인 접근과 Deque의 양쪽 끝 접근을 모두 지원함.
  
```java
import java.util.Deque;
import java.util.LinkedList;

public class DequeExample {
    public static void main(String[] args) {
        Deque<String> deque = new LinkedList<>();
        deque.addFirst("apple");
        deque.addLast("banana");
        System.out.println(deque.removeFirst()); // apple
    }
}
```