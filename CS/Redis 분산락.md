# Redis로 잠금(Lock)
- 여러 서버가 동시에 같은 작업하지 못하게 막는 장치
- 재고 줄이는 작업, 쿠폰 발급 등 여러 서버에서 동시에 하면 꼬일 위험이 있으니 동시에 같은 작업하지 못하도록 Redis가 락을 걸어주는 것.
- 기본개념 : Redis에 특정 키로 잠금 정보를 저장 → 이미 그 키가 존재하면 → 다른 서버는 작업 못함 → 작업 끝나면 → 락 해제
- 잘못 해제되거나 안 해제되면 데드락 생기니까 꼭 TTL + 소유자 확인 + 예외처리 신경 써야 함


~~~ java
// 락 키와 만료 시간 설정
String lockKey = "lock:product:123";
String lockValue = UUID.randomUUID().toString(); // 락 소유자 식별
long expireTime = 5_000; // 5초

// 1. 락 시도 (NX + PX 옵션)
Boolean success = redisTemplate.opsForValue()
    .setIfAbsent(lockKey, lockValue, Duration.ofMillis(expireTime));

if (success) {
    try {
        // 💥 실제 작업 수행
    } finally {
        // 2. 락 해제 (value 비교 후 삭제해야 함!)
        String currentValue = redisTemplate.opsForValue().get(lockKey);
        if (lockValue.equals(currentValue)) {
            redisTemplate.delete(lockKey);
        }
    }
} else {
    // 락 실패, 이미 다른 서버가 하고 있음
}

~~~


| 포인트               | 설명                                  |
| ----------------- | ----------------------------------- |
| `setIfAbsent(NX)` | 이미 키 있으면 못 씀 → 락 걸기 성공 여부 확인        |
| `expireTime(PX)`  | 만약 작업 중 서버가 죽어도 **락 자동 해제됨**        |
| 락 해제 시 value 확인   | 내 락인지 꼭 확인하고 해제해야 함 (다른 서버 락 삭제 방지) |


# Redisson
- Spring에서 쓰기 편하게 Redisson 라이브러리 많이 사용.
- Redisson은 락 재시도, 자동 해제 등 다 해줌.
~~~ java
RLock lock = redissonClient.getLock("lock:coupon:123");

try {
    boolean available = lock.tryLock(3, 5, TimeUnit.SECONDS); // 최대 3초 대기, 5초 유지
    if (available) {
        // 💥 작업
    }
} finally {
    lock.unlock(); // 해제
}

~~~