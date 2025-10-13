# JPA Fetch Join과 페이징을 함께 사용할 때 주의점

## 문제 상황 : 회원 + 주문 (1:N 관계)
- 회원이 주문을 여러 개 할 수 있는 상황이며 아래의 코드가 있다고 가정할 때
~~~ java
SELECT m FROM Member m JOIN FETCH m.orders
~~~

| Member | Order |
| ------ | ----- |
| 슬기     | 주문1   |
| 슬기     | 주문2   |
| 슬기     | 주문3   |
| 토리     | 주문4   |
| 토리     | 주문5   |
| 윤지     | 주문6   |

- 이렇게 하면 슬기는 3줄, 토리는 2줄, 윤지는 1줄
- 총 6줄(row)이 생김. Member는 3명뿐인데 결과는 6줄

### 여기서 페이징 처리를 하면?
~~~ java
query.setFirstResult(0);
query.setMaxResults(3);
~~~
- 위의 6줄 중에 앞에 3줄만 잘라.

| Member | Order |
| ------ | ----- |
| 슬기     | 주문1   |
| 슬기     | 주문2   |
| 슬기     | 주문3   |
- '슬기'만 나옴. 
- 회원 3명의 데이터를 보고 싶었던 건데 실재로는 행 3개만 나옴
- fetch join은 연관된 데이터가 많으면 행이 중복돼서 페이징 기준이 틀어짐 → 결과가 기대보다 작게 나옴

## 해결방법
1. 회원 ID만 먼저 가져오기 (페이징 OK)
2. 그 ID 리스트로 다시 fetch join 해서 데이터 가져오기
~~~ java
-- 1단계: 회원 ID만 페이징
SELECT m.id FROM Member m ORDER BY m.id LIMIT 10

-- 2단계: 그 ID들로 fetch join
SELECT m FROM Member m
JOIN FETCH m.orders
WHERE m.id IN (:ids)
~~~
