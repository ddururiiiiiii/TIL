# 동시성(Concurrency)
- 여러 작업을 한 번에 처리하는 척 하며 번갈아 실행
- CPU 하나가 A → B → A → B 이렇게 번갈아가며 일함


# 병렬성(Parallelism)
- 여러 작업을 진짜 동시에 실행
- CPU 코어가 여러 개여서 A와 B를 동시에 처리


# 💥 동시성과 병렬성에서 발생할 수 있는 문제들
1. 경쟁 조건(Race Condition)
- 두 개 이상의 스레드가 같은 데이터에 동시에 접근하고 수정할 때, 예상치 못한 결과가 나옴
예: 은행 계좌 잔고 동시에 출금 → 최종 잔고 꼬임

2. 데드락(Deadlock)
- A가 B의 자원을 기다리고, B는 A의 자원을 기다림 → 서로 기다리다가 멈춤

3. 라이브락(Livelock)
- 데드락과 비슷하지만, 서로 피해주려고 계속 바뀌기만 하고 진전이 없음

4. 기아(Starvation)
- 특정 스레드가 자원을 계속 못 얻어서 실행 기회조차 못 가짐

----

# 🧯 해결 방법들
1. 동기화(Synchronization)
- synchronized, lock, mutex 등 사용해서 공유 자원 접근 제한
- 한 번에 하나의 스레드만 접근 가능하게 함

2. Atomic 연산 사용
- AtomicInteger, compareAndSet 같은 걸로 경쟁 없이 안전한 연산 수행

3. Lock-Free, Wait-Free 알고리즘
- 가능한 한 락을 사용하지 않고 문제를 해결 (성능 향상 목적)

4. 데드락 예방
- 락을 잡는 순서를 정해두고, 그 순서를 항상 지키게 설계
- 타임아웃 설정해서 오래 기다리면 중단

5. Thread-safe 자료구조 사용
- Java: ConcurrentHashMap, CopyOnWriteArrayList 등