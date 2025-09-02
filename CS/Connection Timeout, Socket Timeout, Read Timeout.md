# Connection Timeout, Socket Timeout, Read Timeout
- 주로 백엔드에서 외부 시스템(예: DB, API 서버 등)과 통신할 때 쓰이는 개념
- **"네트워크를 통한 통신"**에서 자주 등장하는 개념

## 📍 어디서 쓰이는지?
- HTTP 클라이언트 → 외부 API 호출할 때 (예: RestTemplate, WebClient, HttpClient)
- DB 클라이언트 → DB랑 연결할 때 (예: JDBC, MyBatis)
- Redis, Kafka, Elasticsearch 등 외부 시스템 연결 시
즉, 백엔드가 뭔가랑 연결될 때 쓰이는 모든 곳에서 사용!


## Connection Timeout
- 연결 자체를 시도하는 시간
- 예시: 백엔드 서버가 DB나 API 서버랑 연결을 시도하는데, 상대방이 너무 느려서 연결이 안 됨 → 연결 시도하다 시간 초과!

## Read Timeout
- 요청을 보낸 뒤 응답 받는 데 걸리는 시간
- 예시: 백엔드 서버가 외부 API에 요청을 보냈는데, 상대가 응답을 안 해! 계속 기다리고 있어 → 기다리다 시간 초과!

## Socket Timeout
- 흔히 Read Timeout이랑 비슷하게 쓰이는데, 기술적으로는 데이터 읽기/쓰기 작업 중에 대기하는 시간을 의미
- 즉, TCP 소켓 통신 중 **"데이터 단위"**를 읽고 쓰는 데 걸리는 시간
- 실무에서는 보통 "Socket Timeout = Read Timeout"처럼 동의어처럼 사용되기도 함.