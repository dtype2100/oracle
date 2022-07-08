# Programmers Oracle NULL

# 이름이 없는 동물의 아이디

- 이름이 없이 들어온 동물의 ID 조회
- ID 오름차순 정렬

```sql
SELECT ANIMAL_ID 
FROM ANIMAL_INS
WHERE NAME IS NULL
ORDER BY ANIMAL_ID;
```

# 이름이 있는 동물의 아이디

- 이름이 있는 동물의 ID 조회
- ID 오름차순 정렬

```sql
SELECT ANIMAL_ID
FROM ANIMAL_INS
WHERE NAME IS NOT NULL
ORDER BY ANIMAL_ID;
```

# NULL 처리하기

- 생물 종, 이름, 성별  및 중성화 여부를 ID 순서로 조회
- 이름이 없는 동물의 이름은 “No name”

```sql
SELECT ANIMAL_TYPE, NVL(NAME, 'No name') "NAME", SEX_UPON_INTAKE
FROM ANIMAL_INS
ORDER BY ANIMAL_ID;
```