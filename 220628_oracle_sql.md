# scott 계정 생성



```sql
cmd 창에 sqlplus /as sysdba

# 컨테이너 변경
alter session set container=PDBORACLEJAVA

# 변경 후 이름 확인
show con_name
CON_NAME

create user scott identified by tiger; # 사용자 생성
grant connect, resource to scott; # 권한 부여
alter user scott quota 1024m on user; # 사용자 변경

```



# where not

```sql


SELECT * FROM emp;
SELECT ename, job, deptno, hiredate
FROM emp
WHERE not job = 'MANAGER';
```



# between

```sql
SELECT studno, name, weight
FROM student
WHERE weight between 50 and 70;

SELECT * FROM emp;
SELECT empno, ename, sal, deptno
FROM emp
WHERE sal between 1100 and 3000;
```



# IN

```sql
SELECT name, grade, deptno
FROM student
WHERE deptno IN (102, 201);

SELECT * FROM professor;
SELECT profno, name, position, sal
FROM professor
WHERE position in ('교수', '조교수')
```



# between, in 

```sql
SELECT * FROM emp;
SELECT empno, job, sal
FROM emp
WHERE sal between 1500 and 5000 and job in ('ANALYST', 'PRESIDENT');


```



# FROM 두 줄

```sql
SELECT empno, job, sal
FROM emp,
    dept
WHERE sal between 1500 and 5000 
AND job in ('ANALYST', 'PRESIDENT');
```



# like

```sql
SELECT name, grade, deptno
FROM student
WHERE name like '김_영';
```



# ESCAPE 옵션

```sql
# %를 앞, 뒤에 따라 검색 조건이 달라짐

SELECT empno, ename, job, sal
FROM emp
WHERE sal > 1500  
AND ename like '%L%' # 이름에 L 포함. % 앞 뒤로 붙여볼 것
AND job in 'SALESMAN';

SELECT empno, ename, job, sal
FROM emp
WHERE sal > 1500  
AND ename like '%L%L%'
AND job in 'SALESMAN';

SELECT * FROM student;
SELECT name
FROM student
WHERE idnum like '______1%'; # 남자만 출력

SELECT name
FROM student
WHERE name like '황보\_%' escape '\';
```



# NULL

```sql
SELECT nvl(null, 'A', 'B') # Null 판단대상, 대체값. 

SELECT name, position, comm
From professor
WHERE comm = NULL; # 컬럼에 NULL 있는지 비교 검색

SELECT name, position, comm
From professor
WHERE comm IS NULL; # NULL 있는 값 검색

SELECT name, position, comm
From professor
WHERE comm IS NOT NULL; NULL #없는 값 검색

SELECT ename
FROM emp
WHERE ename like 'A%' # 이름이 A로 시작
AND COMM IS NULL; # COMM 없음

SELECT name, sal, comm, sal+comm sal_com # 덧셈 연산 및 별명 짓기
FROM professor;

```



# 연산자 우선순위

1. 괄호

2. 모든 비교 연산자
3. NOT
4. AND
5. OR

```sql
SELECT name, grade, deptno
FROM student
WHERE deptno = 102
AND (GRADe = '1'
OR GRADE = '4');
```



# 집합 연산을 위한 테이블 생성

```sql
CREATE TABLE stud_heavy
AS SELECT *
FROM student
WHERE weight >= 70 AND grade = '1';

CREATE TABLE stud_101
AS SELECT *
FROM student
WHERE deptno = 101 AND grade = '1';
```



# UNION, UNION ALL 연산 비교

```sql
SELECT studno, name
FROM STUD_HEAVY
UNION
SELECT studno, name
FROM stud_101;
10102	박미경
10106	서재진
20102	박동진

SELECT studno, name
FROM STUD_HEAVY
UNION ALL
SELECT studno, name
FROM stud_101;

20102	박동진
10106	서재진
10102	박미경
10106	서재진


# 학생에 sal이 없는 경우, 0으로 설정하고 sal별명 생성
SELECT name, userid, 0 sal 
FROM student
UNION
SELECT name, userid, sal
FROM professor;

# INTERSECT
SELECT name FROM STUD_HEAVY
INTERSECT
SELECT name FROM stud_101;

# MINUS
SELECT studno 학번, name 이름
FROM stud_heavy
MINUS
SELECT studno, name
FROM stud_101;

# MINUS
SELECT name, position
FROM professor
MINUS
SELECT name, position
FROM professor
WHERE position = '전임강사';
```



# sorting(정렬)

```sql
SELECT name, grade, tel
FROM student
ORDER BY name; # asc는 생력 가능. 기본값이기 때문.

SELECT name, grade, tel
FROM student
ORDER BY grade desc;

```





# 문제풀이

```sql

1. 아래 질의는 오류를 포함하고 있습니다. 맞게 수정해서 실행해 보세요.
SELECT name, job, sal*12 AS yearly sal
FROM emp;

SELECT ename, job, sal*12 AS "yearly sal"
FROM emp;

2. 사원 테이블에서 이름에 A를 포함하고 커미션을 받지 않는
사원의 사번, 이름, 급여, 커미션을 출력하세요. (결과가 아래와 같이 나오도록)

EMPNO ENAME SAL COMM
-------- ---------- ---------- ----------
7698 BLAKE 2850
7782 CLARK 2450
7876 ADAMS 1100
7900 JAMES 950

SELECT empno, ename, sal, comm
FROM emp
WHERE ename like '%A%'
AND COMM IS NULL;

3. $1250~$2800을 받고 부서 20이나 30에 속하는 사원의 이름과 급여를
출력하세요. 열의 레이블을 Employee와 Monthly Salary로 하세요.
(결과는 아래와 같도록)

Employee Monthly Salary
---------- --------------
WARD 1250
MARTIN 1250
TURNER 1500
ALLEN 1600

SELECT ename Employee, sal MonthlySalary
FROM emp
WHERE sal between 1250 and 2800
AND (deptno = 20
OR deptno = 30);

4. 학생 테이블에서 이름이 '진'으로 끝나고, 지도교수가 배정되지 않는
101번 학과 학생의 아이디, 이름, 학년, 학과 번호를 출력하여라.

SELECT userid, name, grade, deptno
FROM student
WHERE name like '%진'
AND profno IS NULL
AND deptno = 101;

5. 101번과 202번 학과에 속하지 않는 모든 교수의 이름과 학과번호를
교수명 순으로 정렬되도록 출력하세요.

SELECT name, deptno
FROM professor
WHERE deptno between 201 and 202
ORDER BY name;

6.
업무(job)가 MANAGER이거나 SALESMAN이며 급여가 $1500, $3000 또는 5000이 아닌 모든 사원에 대해서
이름, 업무, 그리고 급여를 출력하세요.(결과가 아래와 같이 나오도록)

ENAME JOB SAL
----------- --------- ----------
JONES MANAGER 2975
BLAKE MANAGER 2850

SELECT ename, job, sal
FROM emp
WHERE (job = 'MANAGER' OR job = 'SALESMAN')
AND (sal != 1500 OR sal != 3000 OR sal != 5000);
```









