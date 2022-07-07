# 220706_oracle

상태: 작성 중

# 롤

- 다수의 사용자와 다양한 권한을 효과적으로 관리하기 위하여 서로 관련된 권한을 그룹화한 개념

```sql
# 시스템 연결
CONNECT system

# 롤 생성
CREATE ROLE hr_clerk
IDENTIFIED BY manager; # 암호지정

# 세션에 권한 부여
GRANT create session TO hr_mgr;

# 객체에 대한 권한 부여
GRANT select, insert, delete, ON student TO hr_clerk;

# 롤 부여
grant hr_clerk to hr_mgr;

# 조회
SELECT * FROM hr.student;
```

# 동의어

- 동의어는 하나의 객체에 대해 다른 이름을 정의하는 방법

```sql
# 전용 동의어 생성 예

SQL> create table project(
  2  project_id number(5) constraint pro_id_pk primary key,
  3  project_namme varchar2(100),
  4  studno number(5),
  5  profno number(5));

SQL> insert into project values(12345, 'portfolil', 10101, 9901);

SQL> select * from project;
```

## 동의어 생성 방법

```sql
SQL> grant select on project to scott;

SQL> conn scott/tiger

create synonym my_project for system.project;

select * from my_project;
```

## 공용 동의어 생성

```sql
SQL> conn system/manager
Connected.
SQL> create public synonym pub_project for project;

Synonym created.

SQL> conn scott/tiger
Connected.
SQL> select * from pub_project;
```

## 동의어 삭제

```sql
DROP SYNONYM synonym # (동의어 명, 전용 동의어 명)

DROP synonym my_project
```

## 동의어 확인

```sql
SELECT * FROM ALL_synonyms
WHERE SYNONYM_NAME = 'MY_PROJECT'
```

# 계층적 질의문

## Top Down

```sql
SELECT deptno, dname, college
FROM department
START WITH deptno = 10
CONNECT BY PRIOR deptno = college;
```

## Bottom Up

```sql
SELECT deptno, dname, college
FROM department
START WITH deptno = 102
CONNECT BY PRIOR college = deptno;
```

## 계층적 질의문 - 레벨별 구분

```sql
SELECT LEVEL
        , LPAD(' ',(LEVEL-1)*2) || dname 조직도
FROM department
START WITH dname = '공과대학'
CONNECT BY PRIOR deptno = college;
```

## 계층 구조에서 가지 제거

- CONNECT BY 를 사용하면 자식 노드까지 동시 삭제

```sql
# 정보미디어학부 제외하고 출력

SELECT deptno, college, dname, loc
FROM department
WHERE dname != '정보미디어학부'
START WITH college is null
CONNECT BY PRIOR deptno = college;

# 정보미디어학부와 속한 모든 학과 제외
SELECT deptno, college, dname, loc
FROM department
START WITH college is null
CONNECT BY PRIOR deptno = college
AND dname != '정보미디어학부';
```

## CONNECT_BY_ROOT

- ROOT부터 시작하기 위해 LEVEL-1 적용

```sql
SELECT LPAD(' ',4*(LEVEL-1)) || ename 사원명
        , empno 사번
        , CONNECT_BY_ROOT empno 최상위사번
        , LEVEL
FROM emp
START WITH job = UPPER('president')
CONNECT BY PRIOR empno = mgr;
```

## CONNECT_BY_ISLEF

```sql
SELECT LPAD(' ',4*(LEVEL-1)) || ename 사원명
        , empno 사번
        , CONNECT_BY_ISLEAF Leaf_YN
        , LEVEL
FROM emp
START WITH job = UPPER('President')
CONNECT BY PRIOR empno = mgr;
```

## SYS_CONNECT_BY_PATH

```sql
SELECT LPAD(' ',4*(LEVEL-1)) || ename 사원명
        , empno 사번
        , SYS_CONNECT_BY_PATH(ENAME,'/') PATH
        , LEVEL
FROM emp
START WITH job = UPPER('President')
CONNECT BY PRIOR empno = mgr;

SELECT LEVEL
        ,SYS_CONNECT_BY_PATH(ENAME, '/') PATH
FROM EMP
WHERE CONNECT_BY_ISLEAF = 1
START WITH mgr IS NULL
CONNECT BY PRIOR empno = mgr;
```

## ORDER SIBLINGS BY

- ORDER BY 를 사용하면 TREE 구조가 깨지기 때문에, 유지를 위해 ORDER SIBLINGS BY를 사용

```sql
# ORDER SIBLINGS BY
SELECT LPAD(' ', 4*(LEVEL-1)) || ename 사원명
        , ename ename2, empno 사번, level
FROM emp
START WITH job = UPPER('President')
CONNECT BY NOCYCLE PRIOR empno = mgr
ORDER SIBLINGS BY ename;

# ORDER BY
SELECT LPAD(' ', 4*(LEVEL-1)) || ename 사원명
        , ename ename2, empno 사번, level
FROM emp
START WITH job = UPPER('President')
CONNECT BY NOCYCLE PRIOR empno = mgr
ORDER BY ename;
```

# 원격 데이터 베이스 엑세스

```sql
C:\oraclexe\app\oracle\product\11.2.0\server\network\ADMIN
```

```sql
XE =
  (DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = DESKTOP-9CT0MUE)(PORT = 1521))
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = XE)
    )
  )
```

- 아래처럼 변경 가능
- XE에 있는 이름 변경
- PORT 번호 변경

```sql
SAMSUNG =
  (DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = DESKTOP-9CT0MUE)(PORT = 1521))
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = XE)
    )
  )
```

- 변경 후 ID/PASSWORD 입력

![Untitled](220706_oracle%20fea6fae9b74c46f4adba03e746fdb37e/Untitled.png)

- 문제 발생 시, Listener 다시 시작

![Untitled](220706_oracle%20fea6fae9b74c46f4adba03e746fdb37e/Untitled%201.png)

## 원격 데이터 베이스 접속

```sql
SQL> conn scott/tiger
Connected.
SQL> conn scott/tiger@xe
Connected.
```

## 서버 shut down

```sql
SQL> conn /as sysdba
Connected.
SQL> shutdown immediate;
Database closed.
Database dismounted.
ORACLE instance shut down.
```

```sql
SQL> conn /as sysdba
Connected to an idle instance.
SQL> ;
  1* drop public synonym pub_project
SQL> startup
ORACLE instance started.

Total System Global Area 1068937216 bytes
Fixed Size                  2260048 bytes
Variable Size             687866800 bytes
Database Buffers          373293056 bytes
Redo Buffers                5517312 bytes
Database mounted.
Database opened.
SQL> conn hr/hr@xe #원격으로 들어올 수 있는지 확인
Connected.
```

# EXPORT & IMPORT

## IMPORT

| FILE | import 될 파일명 |
| --- | --- |
| SHOW | import를 실행하는 대신 화면에 표시할지 여부 |
| IGNORE | 이미 존재하는 객체를 생성하는 경유 등 import가 create 명령을 실행할 떄 발생하는 에러를 무시할지를 나타낸다. |
| GRANTS | 객체들에 대한 권한을 import 할지 여부 |
| FROMUSER | export dump 파일로부터 읽혀져야 하는 객체들을 갖고 있는 사용자 목록 |
| TABLES | import 될 테이블 목록 |

# Export, Import 사용 (TABLE MODE)

```sql
# user에서 EXPDAT.DMP 파일 확인
exp system/manager tables=(hr.emp, hr.dept) grants=y indexes=y

# hr.dmp 확인
exp hr/hr file=hr.dmp tables=emp, dept rows=y compress=y
```

# Import

```sql
C:\Users\user>imp system/manager file=expdat.dmp fromuser=hr touser=system tables=(dept, emp)
```

```sql

C:\Users\user>exp hr/hr file=hr_all.dmp owner=hr grants=y rows=y compress=y
C:\Users\user>imp system/manager file=hr_all.dmp fromuser=hr touser=tiger
```

# 문제 풀이

## 문제

```python
1.system이나 sys 소유의 EMPLOYEE 테이블을 생성하고, 데이터를 하나 입력하라.
Name Null Type
-------- ------------- ------------
ID NUMBER(7)
LAST_NAME VARCHAR2(25)
FIRST_NAME VARCHAR2(25)
DEPT_ID NUMBER(7)

2. system의 employee테이블에 대해 pub_employee라는 공용 동의어를 생성하여라.

3. 2에서 생성한 공용 동의어에 의해 system소유의 employee 테이블을 hr 유저가 조회하도록 하여라.

4. 공용동의어 pub_employee를 삭제 하세요.

5. test라는 테이블 스페이스를 기본 100m로 생성하세요

6. HR의 professor, student 테이블을 백업 받고,

7. test/test333## 이라는 유저 생성후 디폴트 테이블 스페이스는 test,
temporary 테이블 스페이스는 temp을 지정하세요.

8. 7.에서 백업 받은 HR의 professor, student테이블을 test에게 import시키고
import되었는지 확인하세요.

9. 파이썬 문제

홍길동씨의 과목별 점수는 각각 다음과 같다.

과목 점수
국어 80
영어 75
수학 55

홍길동씨의 평균점수를 구하시오.
```

## 실습

```sql
1. # 테이블 생성
SQL> CREATE TABLE EMPLOYEE(
  2  ID NUMBER(7),
  3  LAST_NAME VARCHAR2(25),
  4  FIRST_NAME VARCHAR2(25),
  5  DEEPT_ID NUMBER(7));

Table created.

2. # 동의어 생성
SQL> CREATE PUBLIC synonym pub_employee for employee;

3. # 권한 부여
SQL> grant select on pub_employee to hr;

4. # 동의어 삭제
SQL> DROP PUBLIC SYNONYM pub_employee;

5. # 테이블스페이스 생성
SQL> CREATE TABLESPACE test
  2  DATAFILE 'C:\oraclexe\app\oracle\oradata\XE\test.dbf' size 100m;

6. # 테이블 백업
C:\Users\user>exp hr/hr file=hr.dmp tables=professor, student rows=y, compress=y

7. # 유저 생성
SQL> CREATE USER TEST IDENTIFIED BY TEST333
  2  DEFAULT TABLESPACE TEST
  3  TEMPORARY TABLESPACE TEMP;

8. # 임포트
C:\Users\user>imp system/manager file=test.dmp fromuser=hr touser=test

9. # 파이썬 연습문제
a = 80
b = 75
c = 55
d = (a+b+c)/3
print(d)
```

# Study

```sql
SELECT deptno, dname, college
FROM department
START WITH deptno = 10
CONNECT BY PRIOR deptno = college;

SELECT deptno, dname, college
FROM department
START WITH deptno = 102
CONNECT BY PRIOR college = deptno;

SELECT LEVEL
        , LPAD(' ',(LEVEL-1)*2) || dname 조직도
FROM department
START WITH dname = '공과대학'
CONNECT BY PRIOR deptno = college;

SELECT deptno, college, dname, loc
FROM department
WHERE dname != '정보미디어학부'
START WITH college is null
CONNECT BY PRIOR deptno = college;

SELECT deptno, college, dname, loc
FROM department
START WITH college is null
CONNECT BY PRIOR deptno = college
AND dname != '정보미디어학부';

SELECT LPAD(' ',4*(LEVEL-1)) || ename 사원명
        , empno 사번
        , CONNECT_BY_ROOT empno 최상위사번
        , LEVEL
FROM emp
START WITH job = UPPER('president')
CONNECT BY PRIOR empno = mgr;

SELECT LPAD(' ',4*(LEVEL-1)) || ename 사원명
        , empno 사번
        , CONNECT_BY_ISLEAF Leaf_YN
        , LEVEL
FROM emp
START WITH job = UPPER('President')
CONNECT BY PRIOR empno = mgr;

SELECT LPAD(' ',4*(LEVEL-1)) || ename 사원명
        , empno 사번
        , SYS_CONNECT_BY_PATH(ENAME,'/') PATH
        , LEVEL
FROM emp
START WITH job = UPPER('President')
CONNECT BY PRIOR empno = mgr;

SELECT LEVEL
        ,SYS_CONNECT_BY_PATH(ENAME, '/') PATH
FROM EMP
WHERE CONNECT_BY_ISLEAF = 1
START WITH mgr IS NULL
CONNECT BY PRIOR empno = mgr;

SELECT LPAD(' ', 4*(LEVEL-1)) || ename 사원명
        , ename ename2, empno 사번, level
FROM emp
START WITH job = UPPER('President')
CONNECT BY NOCYCLE PRIOR empno = mgr
ORDER SIBLINGS BY ename;

SELECT LPAD(' ', 4*(LEVEL-1)) || ename 사원명
        , ename ename2, empno 사번, level
FROM emp
START WITH job = UPPER('President')
CONNECT BY NOCYCLE PRIOR empno = mgr
ORDER BY ename;
```