# NOT IN 쿼리를 사용할 때 발생할 수 있는 문제

## NOT IN 쿼리 예시
~~~ sql
SELECT * FROM users
WHERE id NOT IN (SELECT user_id FROM banned_users);
~~~
- → "banned_users에 없는 user만 가져와!" 라는 의미


## ⚠️ 문제점 1: NULL 이 섞여 있으면 결과가 이상해짐
- NOT IN의 서브쿼리 안에 NULL 값이 하나라도 있으면 전체 결과가 아예 안 나올 수도 있음
- 이유 : SQL에서 NULL은 모른다는 뜻, 그러니까 NOT IN의 서브쿼리 안에 NULL 값이 하나라도 있으면 전체 결과가 아예 안 나올 수도 있음
~~~ sql
SELECT * FROM users
WHERE id NOT IN (1, 2, NULL);
~~~
- 이건 절대 true가 될 수 없어 아무 행도 안 나옴

## ⚠️ 문제점 2: 성능 저하
- NOT IN은 인덱스를 잘 못타서 성능이 느려짐
- 특히 서브쿼리가 크거나 연산이 복잡하면 느려짐.

----

## 최적화 방법
### 1. NOT EXISTS로 바꾸기
~~~ sql
SELECT * FROM users u
WHERE NOT EXISTS (
  SELECT 1 FROM banned_users b WHERE b.user_id = u.id
);
~~~
- 이 방식은 NULL이 있어도 안전하고, 인덱스도 잘 타는 경우가 많음!

### 2. LEFT JOIN + IS NULL 방식
~~~ sql
SELECT u.*
FROM users u
LEFT JOIN banned_users b ON u.id = b.user_id
WHERE b.user_id IS NULL;
~~~
-  "조인했는데 아무것도 안 걸린 사람" = 제외 대상에 없는 사람
-  상황에 따라 NOT EXISTS보다 성능이 더 좋을 수도 있음

