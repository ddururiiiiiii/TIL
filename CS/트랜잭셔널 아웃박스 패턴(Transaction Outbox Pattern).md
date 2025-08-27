# 트랜잭셔널 아웃박스 패턴(Transaction Outbox Pattern)


## 트랜잭셔널 아웃박스 패턴 등장하게 된 이유
- 예를 들어 게시글이 작성 완료되면 DB에도 저장을 해야 하고, Kafka에 메시지도 보내서 알람도 전송해야 하는 상황인데 DB에는 잘 저장되었으나 Kafka 메시지는 네트워크 오류 전송으로 실패될 경우
- 사용자 입장에서는 게시글은 작성됐으나 알림은 가지 않은 상태 -> 시스템 일관성이 깨짐
- 이런 문제를 해결하기 위해 트랜잭셔널 아웃박스 패턴이 등장하게 됨

## 트랜잭셔널 아웃박스 패턴(Transaction Outbox Pattern)
- 핵심 아이디어 : Kafka에 바로 보내지 말고, DB에 ‘보낼 메시지’를 함께 저장해라.
- 트랜잭션 시작 - 게시글 저장 - 보낼 "보낼 Kafka 메시지"를 Outbox 테이블에 함께 저장 ( 예: outbox_event 테이블에 JSON 형태로 기록) -> 트랜잭션 커밋
- 여기까지 하면 DB에는 게시글도, 메시지도 같이 저장됨.
- 별도의 백그라운드 작업을 함. (Polling or Change Data Capture(CDC)로 Outbox 테이블을 감시하여, 아직 Kafka에 안 보낸 메시지를 찾아서 전송 -> 전송 완료되면 outbox_event에 상태 업데이트 (예: SENT로 바꾸기)

## 장점
| 문제                   | 트랜잭셔널 아웃박스가 해결하는 방식             |
| -------------------- | ------------------------------- |
| DB는 성공, Kafka는 실패하면? | DB에 메시지를 먼저 저장하니 일관성 유지         |
| Kafka 보내는 작업이 실패해도?  | 메시지는 여전히 Outbox에 있음. 재시도 가능     |
| 분산 트랜잭션 없어도 돼?       | **단일 DB 트랜잭션만 사용**하니까 간편함 |

## 이럴 때 많이 사용
- Kafka, RabbitMQ 등 외부 시스템에 이벤트를 전송해야 할 때
- DB와 메시지 일관성을 유지하고 싶을 때
- 분산 트랜잭션이 부담스러울 때

### Polling?
- 주기적으로 테이블을 확인하는 방법
- 장점: 구현 쉬움 (Spring에서도 쉽게 함)
- 단점: 타이밍 지연이 있음 (최대 대기시간만큼 늦어질 수 있음), 비효율적일 수도 있음
~~~ java
while (true) {
    // 1. 아직 전송 안 된 메시지들 조회 (예: status = 'PENDING')
    List<OutboxEvent> events = outboxRepository.findUnsentEvents();

    // 2. Kafka 전송
    for (event in events) {
        kafkaProducer.send(event.toKafkaMessage());
        event.markAsSent(); // 상태를 SENT로 바꿈
        outboxRepository.save(event);
    }

    // 3. 잠깐 대기 (예: 5초)
    Thread.sleep(5000);
}
~~~

### Change Data Capture (CDC)
- DB에서 테이블이 변경된 걸 감지해서 바로 이벤트로 보내는 방식
- DB는 내부적으로 변경 로그(Change Log 또는 binlog)를 유지함
- 이 로그를 실시간으로 읽어서 "Outbox 테이블에 새로운 row 생겼네!" → 바로 Kafka로 전송
- 대표적인 CDC 도구 :Debezium 
- 장점: 거의 실시간으로 동작, polling보다 빠르고 효율적
- 단점: 셋업이 복잡함 (Kafka + Debezium + Kafka Connect + Zookeeper 등)

----

- 👉 초기에는 Polling 방식으로 구현하고, 나중에 MSA 확장되거나 트래픽 많아지면 CDC 고려/