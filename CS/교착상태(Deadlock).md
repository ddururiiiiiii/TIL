# 교착 상태(deadlock)
- 두 개 이상의 작업이 서로 상대방의 작업이 끝나기만을 기다리고 있어 결과적으로 아무것도 완료되지 못하는 상태

## 교착상태 발생 조건
- 상호 배제(mutual exclusion) 는 한 프로세스가 사용하는 자원을 다른 프로세스가 사용할 수 없는 경우
- 점유 대기(hold and wait) 는 자원을 할당받은 상태에서 다른 자원을 할당받기를 기다리는 상태
- 비선점(non-preemption) 은 자원이 강제적으로 해제될 수 없으며 점유하고 있는 프로세스의 작업이 끝난 이후에만 해- 제되는 것
- 원형 대기(circular wait) 은 프로세스들이 원의 형태로 자원을 대기하는 것

## 교착상태 예방/해결 방법 (Java)

1. 🔒 락 획득 순서 통일
- 항상 동일한 순서로 락을 획득하게 하면 교착상태가 안 생김!

~~~java
// 모든 스레드가 A → B 순서로 락을 건다
synchronized(lockA) {
    synchronized(lockB) {
        // do something
    }
}
~~~


2. ⏱️ tryLock() 사용 (타임아웃 설정)
ReentrantLock의 tryLock() 사용하면 잠시 기다렸다가 실패 가능
→ 락 획득 실패하면 포기하고 다시 시도 or 우회 가능

~~~java
if (lockA.tryLock(1, TimeUnit.SECONDS)) {
    try {
        if (lockB.tryLock(1, TimeUnit.SECONDS)) {
            try {
                // 작업 수행
            } finally {
                lockB.unlock();
            }
        }
    } finally {
        lockA.unlock();
    }
}
~~~

3. 🧠 락을 적게 써라 (Lock Granularity 최소화)
- 필요한 부분만 synchronized 걸고, 전체에 락 걸지 않기!
- 락의 범위가 크면 교착 가능성도 커지기 때문.

4. 📊 교착상태 감지 도구 사용
- JConsole, VisualVM, jstack 등으로 스레드 덤프 확인해서 누가 어디서 멈췄는지 추적 가능.