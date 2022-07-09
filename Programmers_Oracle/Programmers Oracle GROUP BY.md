# Programmers Oracle GROUP BY

상태: 최종 점검

# 고양이와 개는 몇 마리 있을까

- 고양이와 개가 각각 몇 마리인지 조회
- 개보다 고양이를 먼저 조회

```sql
SELECT ANIMAL_TYPE, COUNT(*) 
FROM ANIMAL_INS
GROUP BY ANIMAL_TYPE
ORDER BY ANIMAL_TYPE;
```

# 동명 동물 수 찾기

- 두 번 이상 쓰인 이름과 해당 이름이 쓰인 횟수 조회
- 이름이 없는 동물은 제외
- 이름 순서로 정렬

```sql
SELECT NAME, COUNT(NAME)
FROM ANIMAL_INS
GROUP BY NAME
HAVING COUNT(NAME) > 1
ORDER BY NAME;
```

# 입양시각 구하기(1)

- 09:00 ~ 19:59까지 각 시간대에 입양이 얼마나 발생했는지 조회

```sql
SELECT TO_CHAR(DATETIME, 'HH24') "HOUR", COUNT(*) "COUNT"
FROM ANIMAL_OUTS
WHERE TO_CHAR(DATETIME, 'HH24') BETWEEN 09 AND 19
GROUP BY TO_CHAR(DATETIME, 'HH24')
ORDER BY TO_CHAR(DATETIME, 'HH24');

SELECT TO_CHAR(DATETIME, 'HH24') AS HOUR, COUNT(*) AS COUNT
FROM ANIMAL_OUTS
WHERE TO_CHAR(DATETIME, 'HH24') BETWEEN 09 AND 19
GROUP BY TO_CHAR(DATETIME, 'HH24')
ORDER BY TO_CHAR(DATETIME, 'HH24');
```

# 입양시각 구하기(2) (진행중)

- 0~23시 까지 각 시간대별로 입양이 얼마나 발생했는지 조회

```sql

```