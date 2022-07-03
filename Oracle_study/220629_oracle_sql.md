# ORDER BY

```sql
SELECT ename, job, deptno, sal
FROM emp
ORDER BY deptno, sal DESC;

SELECT ename, job, deptno, sal
FROM emp
ORDER BY deptno, sal;

SELECT * FROM emp;
SELECT ename, deptno
FROM emp
WHERE deptno IN (10, 30)
ORDER BY ename;

SELECT ename, deptno
FROM emp
WHERE deptno IN (10, 30)
ORDER BY deptno;

SELECT ename, deptno, sal
FROM emp
WHERE deptno IN (10, 30)
ORDER BY sal DESC;

SELECT ename, hiredate
FROM emp
WHERE HIREDATE like '82%'
ORDER BY HIREDATE;

SELECT ename, sal, comm
FROM emp
WHERE comm >= sal*0.2
AND deptno = 30;
```



# SQL PLUS

```sql
conn hr/hr
ed # 에디터 열림. 오타가 나면 에디터 열기.
l(L) # 직전에 쳤던 명령어
# 마지막에 ;(세미콜론) 없음

INSERT INTO department valuse(301, '제어게측학과', 200, '5호관');
commit; # COMMIT는 영구저으로 저장
AUTOCOMMIT 명령문들의 처리 결과를 제어하는 변수
- set autocommit on 
- set autocommit off 하면 commit; 쳐야함

HEADING off / on # off일 때 칼럼 제목 출력하지 않음

set linesize 100 # 라인 사이즈 100으로 설정
set * from professor;

set pause on / off # 한 페이지씩 나누어서 출력하기 위한 변수

set time on / off # 시간 표시

set timing on / off 
- SQL 명령문을 실행하는데 소요된 시간을 출력하기 위한 변수
- 시간: '시:분:초:밀리초'형식으로 표현
- 튜닝을 위한 가장 기초적인 기법

# format
column name format a20 # a사이즈
column 컬럼명 format a20 # a사이즈

column sal format 0,000,000 # , 콤마 삽입
column sal format 99999 # 원상복귀

column name # 컬럼에 설정된 값 확인
column name CLEAR # 컬럼의 모든 설정 해제

col # 모든 컬럼 확인
clear col # 컬럼 설정값 소거

# SQL Plus 편집 명령어

L # 이전 리스트
/ # 이전 출력값

# n text
ex) 1 select name
-> select name
ex del 숫자 # 원하는 행 삭제

cle scr # 화면 깨끗하게 정리

a and name like '%진' # append
c/and name like '%진' # change
c/잘못된 부분/잘못된 부분 수정

cl buff # clear buff
r # 명령어 및 출력값 모두 확인

sav C:\test\저장할 파일명
save C:\test\저장할 파일명

sav C:\test\저장할 파일명 repl # 덮어쓰기 (replace)
sav C:\test\저장할 파일명 append # 파일 추가

get C:\test\저장한 파일명 # 저장한 파일 가져오기

# spool: 입력한 모든 명령어 저장
spool C:\test\저장한 파일명
spool C:\저장한 파일명\저장한 파일명
spool off # 종료

@ 파일 명 (.sql) # 이전에 저장한 파일 안에 명령어 실행

# 운영체제 명령어
host # 운영체제 명령어
exit # 운영체제에서 sql로 이동

```



# INITCAP

```sql
SELECT name, userid, INITCAP(userid)
FROM student
WHERE name = '김영균'
```



# LOWER, UPPER

```sql
SELECT userid, LOWER(userid), UPPER(userid)
FROM student
WHERE studno = 20101;

SELECT empno, ename, job, deptno
FROM emp
WHERE LOWER(ename) = 'adams';

SELECT * FROM emp;
SELECT INITCAP(ename) "Name", LENGTH(ename) "Lenght"
FROM emp
WHERE ename like 'A%'
OR ename like 'J%'
OR ename like 'M%'
ORDER BY ename;
```



# CONCAT

```sql
SELECT concat(concat(name, '의 직책은 '),position)
FROM professor;

SELECT concat(name, position)
FROM professor;

SELECT name||position
FROM professor;
```



# SUBSTR

```sql
# 문자열 자르기
SELECT ename,SUBSTR(ename, 3)
FROM emp;
------------
ex)
SMITH	ITH
ALLEN	LEN
WARD	RD

SELECT ename,SUBSTR(ename, 3, 5)
FROM emp;
------------
ex)
SMITH	ITH
ALLEN	LEN
WARD	RD

SELECT ename,SUBSTR(ename, 1, 2)
FROM emp;
------------
ex)
SMITH	SM
ALLEN	AL
WARD	WA


SELECT name, idnum, SUBSTR(idnum, 1,6) birth_date,
                    SUBSTR(idnum, 3, 2) Birth_date
FROM student
WHERE grade = '1';

SELECT name, idnum, SUBSTR(idnum, 1,6) birth_date,
                    SUBSTR(idnum, 7, 2) Birth_date
FROM student
WHERE grade = '1'
AND SUBSTR(idnum, 7, 1) = '1';

SELECT INITCAP(ename) "Name", LENGTH(ename) "Lenght"
FROM emp
WHERE SUBSTR(ename, 1, 1) in ('J', 'A', 'M');

```



# LPAD, RPAD

```sql
SELECT name, weight, RPAD(weight, 6, '*')
FROM student;

SELECT name, weight, LPAD(weight, 6, '*')
FROM student;

--------------------
전인하	72	72****
이동훈	64	64****
박미경	52	52****

# 몸무게가 두 자리 수일 경우, 설정값이 6이면 몸무게 이후 여섯 번째 자리까지 * 표시
# 원본값에 append하는 방식으로 추가

SELECT position, Lpad(position, 10, '*') lpad_position,
userid, RPAD(userid, 12, '+')rpad_userid
FROM professor;
```



# LREIM, RTRIM

```sql
SELECT empno, LTRIM(empno, '7') # 해당 컬럼 안에 숫자 '7' 제거
FROM emp;
--------------------
7369	369
7499	499
7521	521

SELECT empno, RTRIM(empno, '9') # 해당 컬럼 안에 숫자 '9' 제거
FROM emp;
--------------------
7369	736
7499	74
7521	7521

SELECT dname, RTRIM(dname, '과')
FROM department;

SELECT RTRIM('xxxy', 'y')
FROM DUAL;

SELECT LTRIM('xxxy', 'xxx')
FROM DUAL;
```





# INSTR

```sql
SELECT INSTR('CORPORATE FLOOR', 'OR')
FROM DUAL;

SELECT INSTR('CORPORATE FLOOR', 'OR', 3, 2)
FROM DUAL;

SELECT INSTR('CORPORATE FLOOR', 'OR', -3, 2)
FROM DUAL;

SELECT dname, INSTR(dname, '과')
FROM department;
```



# 문제풀이

```sql
1. 사원테이블에서 사원명 칼럼의 별명은 Name, 급여*12는
Annual Salary로 부여하여 출력해 보세요. (아래와 같이 나오도록)
Name Annual Salary
---------- -------------
SMITH 9600
ALLEN 19200
WARD 15000

SELECT * FROM emp;
SELECT ename "Name", sal*12 "Annual salary"
FROM emp;

2. 사원테이블의 사원명과 급여를 아래와 같은 포맷으로 출력해 보세요.
MONTHLY
--------------------------------------
SMITH: 1 Month salary = 800
ALLEN: 1 Month salary = 1600
WARD: 1 Month salary = 1250

SELECT concat(concat(ename, ' Monthly = '), sal)
FROM emp;

3. 사원테이블에서 급여가 $1,500 ~ $5,000이고 직무가 PRESIDENT나 ANALYST가
아닌 모든 사원에 대해 사번, 이름, 직무, 급여를 직무,급여의 오름차순으로 정렬 하세요.
(아래와 같이 나오도록)

EMPNO ENAME JOB SAL
-------- ---------- --------- ----------
7782 CLARK MANAGER 2450
7698 BLAKE MANAGER 2850
7566 JONES MANAGER 2975
7844 TURNER SALESMAN 1500
7499 ALLEN SALESMAN 1600

SELECT empno, ename, job, sal
FROM emp
WHERE sal between 1500 and 5000
AND JOB not in ('PRESIDENT', 'ANALYST')
ORDER BY job, sal;

4. 사원 테이블에서 2월에 입사한 사원을 출력해 보세요.(substr 사용)
SELECT * FROM emp;
SELECT ename, hiredate
FROM emp
WHERE SUBSTR(hiredate, 4, 2) = 02;

5. 사원 테이블에서 20이나 30번 부서에 속하고
이름의 마지막 글자에 'S'자를 포함한 사원들 중에서
마지막 'S'를 제거해 보세요.

ENAME RTRIM
-------------------- --------------------
JONES JONE
ADAMS ADAM
JAMES JAME

SELECT RTRIM(ename, 'S')
FROM emp
WHERE deptno in(20, 30)
AND SUBSTR(ename, -1) in ('S');

```

