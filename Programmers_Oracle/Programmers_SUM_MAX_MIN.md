# Programmers SUM, MAX, MIN 문제풀이

# 최댓값 구하기

- 가장 최근 들어온 동물 언제 들어왔는지 조회
- MAX를 사용하면 가장 최근 시간 조회 가능

```sql
SELECT MAX(DATETIME)
FROM ANIMAL_INS;
```

# 최솟값 구하기

- 가장 먼저 들어온 동물이 언제 왔는지 조회
- MIN을 사용하면 가장 먼저 들어온 시간 조회 가능

```sql
SELECT MIN(DATETIME)
FROM ANIMAL_INS;
```

# 동물 수 구하기

- 몇 마리가 들어왔는지 조회
- COUNT(*) 사용하면 전체 수 조회 가능

```sql
SELECT COUNT(*)
FROM ANIMAL_INS;
```

# 중복 제거

- 전체 동물 이름 조회
- 중복 제거
- NULL 집계하지 않음

```sql
SELECT COUNT(DISTINCT(NAME))
FROM ANIMAL_INS
WHERE NAME IS NOT NULL;
```