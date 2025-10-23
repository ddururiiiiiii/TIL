# 열 기반 DB와 행 기반 DB

## 행 기반 DB (Row-based DB)
- 대표: MySQL, PostgreSQL, Oracle 등 대부분의 전통적인 RDB
- 한 줄(row)의 데이터를 통째로 저장
- 하나의 레코드(고객 1명, 주문 1건 등)를 빠르게 저장하거나 읽을 수 있음
- 쓰기/삽입이 빠름
- "한 사람의 정보" 같은 걸 가져올 땐 최적
- 실시간 트랜잭션 처리에 강함 (OLTP)

- ex. 
| 이름 | 나이 | 이메일                                   |
| -- | -- | ------------------------------------- |
| 슬기 | 30 | [sgi@email.com](mailto:sgi@email.com) |
→ 이걸 저장할 때, 이 한 줄 전부를 같이 저장함


----


## 열 기반 DB (Column-based DB)
- 대표: Apache Cassandra, ClickHouse, Amazon Redshift, BigQuery 등
- 각 열(column) 단위로 따로 저장
- "특정 열의 모든 값"을 한꺼번에 처리하기 쉬움
- 열별로 묶어서 저장하니까 같은 타입의 데이터만 모여 있어서 압축도 잘 됨
- 통계/집계 쿼리 빠름 (AVG, SUM, COUNT, GROUP BY 등)
- 빅데이터 분석에 강함 (OLAP)
- 단일 행 전체 조회에는 비효율적일 수 있음

~~~ text
[30, 28, 42, 31, ...]
~~~
