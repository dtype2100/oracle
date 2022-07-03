# ROUND

```sql
SELECT name, sal, sal/22, ROUND(sal/22), ROUND(sal/22,2), ROUND(sal/22, -1)
FROM professor
WHERE deptno = 101;
```



# TRUNC

```sql
# 절삭한 값 계산
SELECT name, sal, sal/22, TRUNC(sal/22), ROUND(sal/22, 2), TRUNC(sal/22,-1)
FROM professor
WHERE deptno = 101;
```



# MOD

```sql
# 나눈 나머지 값 계산
SELECT name, sal, comm, MOD(sal, comm)
FROM professor
WHERE deptno = 101;
```

 

# CFIL, FLOOR

```sql
# CFIL: 가장 작은 정수 출력
# FLOOR: 12.345보다 작은 정수 중 가장 큰 정수 출력
SELECT CEIL(19.7), FLOOR(12.345)
FROM dual;
```



# 날짜 함수 종류

```sql
SYSDATE: 시스템의 현재 날짜
MONTHS_BETWEEN: 날짜와 날짜 사이의 개월을 계산
ADD_MONTHS: 날짜에 개월을 더한 날짜 계산
NEXT_DAY: 날짜 후의 첫 day의 요일 계산
LAST_DAY: 날짜가 속한 달의 마지막 날짜 계산
ROUND: 날짜 반올림
TRUNC: 날짜 절삭
```



# 날짜

```sql
SELECT name, hiredate, hiredate+30, hiredate+60
FROM professor
WHERE profno = 9908;
```



# MONTHS_BETWEEN, ADD_MONTHS

```sql
# MONTHS_BETWEEN: 날짜와 날짜 사이의 개월 계산
# ADD_MONTHS: 날짜에 개월을 더한 날짜 계산.

SELECT profno, hiredate, MONTHS_BETWEEN(SYSDATE, hiredate) 
        TENURE, ADD_MONTHS(hiredate, 6) REVIEW
FROM professor
WHERE MONTHS_BETWEEN(SYSDATE, hiredate) < 360;
```



# LAST_DAY, NEXT_DAY

```sql
# NEXT_DAY: 날짜 후의 첫 day의 요일 계산
# LAST_DAY: 날짜가 속한 달의 마지막 날짜 계산

SELECT SYSDATE, LAST_DAY(SYSDATE), NEXT_DAY(SYSDATE, '일')
FROM dual;

SELECT SYSDATE, LAST_DAY(SYSDATE), NEXT_DAY(SYSDATE, 1)
FROM dual;
```



# ROUND, TRUNC

```sql
# ROUND: 날짜 반올림
# TRUNC: 날짜 절삭
# TO_CHAR: 숫자를 문자열로 전환해주는 함수

SELECT TO_CHAR(SYSDATE, 'YY/MM/DD HH24:MI:SS') NORMAL,
        TO_CHAR(TRUNC(SYSDATE), 'YY/MM/DD HH24:MI:SS') TRUNC,
        TO_CHAR(ROUND(SYSDATE), 'YY/MM/DD HH24:MI:SS') ROUND
FROM dual;

SELECT TO_CHAR(hiredate, 'YY/MM/DD HH24:MI:SS') hiredate,
        TO_CHAR(round(hiredate, 'dd'), 'YY/MM/DD') round_dd,
        TO_CHAR(round(hiredate, 'mm'), 'YY/MM/DD') round_mm,
        TO_CHAR(round(hiredate, 'yy'), 'YY/MM/DD') round_yy
FROM professor
WHERE deptno = 101;
```



# 묵시적인 데이터 타입

1. 잘못된 데이터 타입을 입렫해도 SQL이 적합한 데이터 타입으로 전환





# 명시적인 데이터 타입

```sql
To_CHAR: 숫자, 날짜 타입을 문자로 전환
TO_NUMBER:문자열을 숫자로 전환
TO_DATE: 문자열을 날짜로 전환

SELECT studno, TO_CHAR(birthdate, 'YY-MM') birthdate
FROM student
WHERE name = '전인하';

SELECT name, grade, TO_CHAR(birthdate, 'DAY month DD, YYY') birthdate
FROM student
WHERE deptno = 102;
```



# 시간 표현

```sql
AM, PM: AP, PM

A.M: A.M

A.P: A.P

HH or HH12: 시각(1-2)

HH24: 24시간(0-23)

MI: 분

SS: 초

SELECT name, TO_CHAR(hiredate, 'MONTH dd, YYYY HH24:MI:SS PM') HIREDATE
FROM professor
WHERE deptno = 101;

# Mon: 월
# DDTH: 날짜TH
# YYYY: 연도
SELECT name, position, TO_CHAR(hiredate, 'Mon "the" DDTH "of" YYYY') HIREDATE
FROM professor
WHERE deptno = 101;

----------------------------------------
김도훈	교수	6월  the 24TH of 1982
성연희	조교수	5월  the 17TH of 1993
이만식	부교수	9월  the 13TH of 1988
전은지	전임강사	6월  the 01ST of 2001

```



# 숫자를 문자 형식으로 변환

```SQL
SELECT name, sal, comm, TO_CHAR((sal+comm)*12, '9,999') anual_sal
FROM professor
WHERE comm IS NOT NULL;


# *를 사용하여 #####이 나온 경우, 자릿 수가 잘못 입력되어 + 등으로 변경 필요
SELECT name, sal, comm, TO_CHAR((sal*comm)*12, '9,999') anual_sal
FROM professor
WHERE comm IS NOT NULL;
-----------------------
김도훈	500	20	######
성연희	360	15	######
권혁일	450	25	######
남은혁	400	17	######
```



# TO_NUMBER

```sql
SELECT TO_NUMBER('1') num
FROM dual;
```



# TO_DATE

```sql
SELECT name, hiredate
FROM professor
WHERE hiredate = TO_DATE('6월 01, 01', 'MONTH DD, YY');
DESC professor

SELECT name, hiredate
FROM professor
WHERE hiredate = TO_DATE('june 01, 01', 'MONTH DD, YY');
DESC professor
```



# 영어, 한국어로 언어 설정

```sql
ALTER SESSION SET NLS_LANGUAGE = KOREAN;
ALTER SESSION SET NLS_LANGUAGE = ENGLISH;
```



# 살아온 날짜, 개월 계산

```sql
SELECT TRUNC(SYSDATE - TO_DATE('2000-02-02', 'YYYY-MM-DD')) "Lived day"
FROM dual;

SELECT TRUNC(SYSDATE - TO_DATE('2000-02-02', 'YYYY-MM-DD')) "Lived day", TRUNC(MONTHS_BETWEEN(SYSDATE, TO_DATE('2000-02-02','YYYY-MM-DD'))) "Lived Month"
FROM dual;
```



# 중첩 함수 예

```sql
SELECT TO_CHAR(TO_DATE(SUBSTR(idnum, 1, 6), 'YYMMDD')) BIRTHDATE
FROM student;
```



# NVL

* NVL은 NULL을 0 또는 다른 값으로 변환하기 위한 함수

```sql
SELECT name, position, sal, comm, sal+comm,
        sal+NVL(comm, 0) sa1, NVL(sal+comm, sal) s2
FROM professor
WHERE deptno = 201;

SELECT sal, NVL(TO_CHAR(comm, 'No Commission') 
FROM emp;
```



# NVL2

```sql
# null이 아니면, sal+comm
# null이면 sal

SELECT name, position, sal, comm,
        NVL2(comm, sal+comm, sal) total
FROM professor
WHERE deptno = 102;
```



# NVL, NVL2

```SQL
SELECT ENAME, SAL, COMM
    , SAL+COMM
    , NVL2(COMM, SAL+COMM, SAL)
    , SAL+NVL(COMM,0)
from EMP;
```



# NULLIF 

```sql
# 동일하면 NULL 반환
# 동일하지 않으면 첫 번째 값 반환

SELECT name, userid, LENGTHB(name), LENGTHB(userid),
        NULLIF(LENGTHB(name), LENGTHB(userid)) nullif_result
FROM professor;
```



# COALESCE

```sql
# NULL 이면 0출력
# NULL 아니면, sal 출력

SELECT name, comm, sal, COALESCE(comm, sal, 0) CO_RESULT
FROM professor;
```



# DECODE

```sql
# 101이면 컴퓨터공학, 아니면 기계공학 또는 ETC

SELECT name, deptno,
            DECODE(deptno, 101, '컴퓨터공학과', 102, '멀티미디어학과',
                            201, '전자공학과', '기계공학과') DNAME
FROM professor;

SELECT studno, name, DECODE(deptno, 101, 'Coumputer Science', 'ETC') "학과명"
FROM student
WHERE name <> '황보_정호'; # 황보_정호 제거
```



# CASE

```sql
# 101이면 10%, 102면 20%, 201이면 30%, 나머지(ELSE)는 0
# END 새로운 컬럼 형성
# CASE가 DECODE와 다른 점은 다양한 연산이 가능하다는 점

SELECT name, deptno, sal,
    case WHEN DEPTNO = 101 THEN sal*0.1
        WHEN DEPTNO = 102 THEN sal*0.2
        WHEN DEPTNO = 201 THEN sal*0.3
        ELSE 0
    END bobus
FROM professor;
```



# 분기 구하기

```sql
SELECT name, studno,
        CASE WHEN to_char(birthdate, 'q') = '1' THEN '1/4'
            WHEN to_char(birthdate, 'q') = '2' THEN '2/4'
            WHEN to_char(birthdate, 'q') = '3' THEN '3/4'
            WHEN to_char(birthdate, 'q') = '4' THEN '4/4'
            END 분기
FROM student
WHERE name <> '황보_정호';
```



# COUNT

```sql
SELECT COUNT(comm)
FROM professor
WHERE deptno = 101;

SELECT COUNT(*)
FROM professor
WHERE deptno = 101 AND comm IS NOT NULL;

SELECT COUNT(job)
FROM emp;

SELECT COUNT(DISTINCT(job)) # 중복 제거
FROM emp;
```



# AVG, SUM

```sql
SELECT AVG(weight), SUM(weight)
FROM student
WHERE deptno = 101;
```



# MIN, MAX

```sql
SELECT MAX(height), MIN(height)
FROM student
WHERE deptno = 101;
```



# STDDEV, VARIANCE

```sql
SELECT STDDEV(sal), VARIANCE(sal)
FROM professor;
```



# MAX, MIN, SUM, AVG

```sql
SELECT ROUND(MAX(sal), 0) "Maximum", ROUND(MIN(sal),0) "Minimum",
	ROUND(SUM(sal),0) "Sum", ROUND(AVG(sal),0) "Average"
FROM emp;
```



# GROUP BY

```sql
# 1개 컬럼
SELECT deptno, COUNT(*), COUNT(comm)
FROM professor
GROUP BY deptno;

SELECT deptno, AVG(sal), MIN(sal), MAX(sal)
FROM professor
GROUP BY deptno;

# 확인
SELECT deptno, profno, sal
FROM professor
ORDER BY deptno;

# 다중 컬럼
SELECT deptno, grade, COUNT(*), ROUND(AVG(weight))
FROM student
GROUP BY deptno, grade;

```



# ROLLUP

```sql
#그룹한 부분의 합 계산

SELECT deptno, SUM(sal)
FROM professor
GROUP BY ROLLUP(deptno);

SELECT deptno, position, COUNT(*)
FROM professor
GROUP BY ROLLUP(deptno, position);
```



# CUBE

```sql
# 그룹 조합 생성

SELECT deptno, position, count(*)
FROM professor
GROUP BY CUBE(deptno, position);
```



# GROUPING

```sql
# ROLLUP, CUBE 등의 연산자로 생성된 그룹 조합에서 사용되었는지 여부 1 또는 0으로 반환
# 사용하면 0, 사용하지 않으면 1

SELECT deptno, grade, COUNT(*),
        GROUPING(deptno) grp_dno,
        GROUPING(grade) grp_grade
FROM student
GROUP BY ROLLUP(deptno, grade);
```



# GROUPING SETS 

```sql
# GROUP BY, GROUPING SETS 비교

# GROUP BY
SELECT deptno, grade, null, count(*)
FROM student
GROUP BY deptno, grade
UNION ALL
SELECT deptno, null, TO_CHAR(birthdate, 'YYYY'), COUNT(*)
FROM student
GROUP BY deptno, TO_CHAR(birthdate, 'YYYY');

# GROUPING SETS
SELECT deptno, grade, TO_CHAR(birthdate, 'YYYY') birthdate, COUNT(*)
FROM student
GROUP BY GROUPING SETS((deptno, grade), (deptno, TO_CHAR(birthdate, 'YYYY')));
```



# 문제 연습

```sql
1. 
SELECT MAX(sal) - MIN(sal) "DIFFERENCE"
FROM emp;


2.
SELECT ename, hiredate,TO_CHAR(hiredate, 'Day'), TO_CHAR(hiredate -1, 'D')
FROM emp;

```







