# 220705_oracle

상태: 작성 중

# comments

```sql
# 코멘트 생성
comment on table address
is '고객 주소록을 관리하기 위한 테이블';

# 코멘트 조회
SELECT COMMENTS
FROM user_tab_comments
where table_name = 'ADDRESS';

# 코멘트 삭제
COMMENT ON COLUMN ADDRESS.NAME is '';
```

# 데이터 사전

- 테이블 집합
- 사전 내용은 오라클 서버만 수정 가능

## USER_ 데이터 사전 뷰

```sql
# USER_ 데이터 사전 뷰
# 자신이 생성한 테이블, 인덱스, 뷰, 동의어 등 객체나 해당 사용자에게 부여된 권한 정보 조회
SELECT TABLE_NAME FROM USER_TABLES;
```

## ALL_ 데이터 사전 뷰

- 접근할 수 있는 모든 객체에 대한 정보 조회 가능

```sql
SELECT OWNER, TABLE_NAME FROM ALL_TABLES;
```

## DBA_ 데이터 사전 뷰

- 데이터 베이스 접근 권한자만 접근 가능한 사전 뷰

```sql
SELECT OWNER, TABLE_NAME FROM DBA_TABLES;
```

```sql
conn system/manager;
```

# 데이터 무결성

## 데이터 무결성 제약조건의 개념

- 데이터 정확성과 일관성을 보장

## 데이터 무결성 제약조건의 종류

- NOT NULL: 열이 NULL을 포함할 수 없음
- 고유키(unique key): 테이블의 모든 행에서 고유한 값을 갖는 열 또는 열 조합을 지정합니다.
- 기본키(primary key): 해당 칼럼 값은 반드시 존재해야 하며 유일해야함. UNIQUE, NOT NULL 제약조건을 결합한 형태
- 참조키(foreign key) : 한 열과 참조된 테이블의 열간에 외래 키 관계를 설정하고 시행
- CHECK: 해당 컬럼에 저장 가능한 데이터 값의 범위나 조건 지정

## 무결성 제약조건 생성문 키워드

- ON DELETE CASCADE: 부모 테이블에서 왜래 키가 참조하는 기본키나 고유 키를 포함한 행을 삭제할 경우, 자식 테이블의 왜래 키를 포함하는 행도 삭제
- USING INDEX: 기본키나 고유 키 무결성 제약조건 생성 시 묵시적으로 생성되는 인덱스에 대한 스토리지 파라미터 정의
- NOT DEFERRABLE
- DEFERRABLE
- INITIALLY IMMEDIATE
- INITIALLY DEFERRED

## 무결성 제약조건 생성 방법

```sql
# 강좌(SUJECT) 테이블 인스턴스
CREATE TABLE SUBJECT
(SUBNO NUMBER(5)
    CONSTRAINT SUBJECT_NO_PK PRIMARY KEY
    DEFERRABLE INITIALLY DEFERRED
    USING INDEX TABLESPACE INDX,
SUBNAME VARCHAR2(20)
    CONSTRAINT SUBJECT_NAME_NN NOT NULL,
TERM VARCHAR(1)
    CONSTRAINT SUBJECT_TERM_CK CHECK (TERM IN ('1', '2')),
    RTPE VARCHAR2(1));

# 수강(SUGANG) 테이블 인스턴스
CREATE TABLE SUGANG
(STUDNO NUMBER(5)
    CONSTRAINT SUGANG_STUDNO_FK REFERENCES STUDENT(STUDNO),
SUBNO NUMBER(5)
    CONSTRAINT SUGANG_STUDNO_FK REFERENCES SUBJECT(SUBNO),
REGDATE DATE,
RESULT NUMBER(3),
    CONSTRAINT SUGANG_PK PRIMARY KEY(STUDNO, SUBNO));
```

# 참고

1. SYSTEM 구매
2. OS INSTALL
3. DBMS 선택(ORACLE…)
4. CREATE TABLE SPACE
5. CREATE USER
6. DATA BACKUP → IMPORT 
7. CREATE TABLE
8. INSERT DATA

### TABLE SPACE 만들기

create tablespace indx
datafile 'C:\oraclexe\app\oracle\oradata\XE\indx.dbf' size 100m;

## 무결성 제약 조건 조회

```sql
# IN 안에 필요한 제약 조건 추가하며 조회

SELECT CONSTRAINT_NAME, CONSTRAINT_TYPE
FROM USER_CONSTRAINTS
WHERE TABLE_NAME IN ('SUBJECT', 'SUGANG'); 
```

## 무결성 제약 조건 추가

```sql
ALTER TABLE STUDENT
ADD CONSTRAINT STUD_IDNUM_UK UNIQUE(IDNUM);

ALTER TABLE STUDENT
MODIFY (NAME CONSTRAINT STUD_NAME_NN NOT NULL);

ALTER TABLE DEPARTMENT
ADD CONSTRAINT DEPT_PK PRIMARY KEY(DEPTNO);

ALTER TABLE STUDENT ADD CONSTRAINT STUD_DEPTNO_FK
FOREIGN KEY(DEPTNO) REFERENCES DEPARTMENT(DEPTNO);
```

## NOT NULL 추가

```sql
ALTER TABLE department
modify (dname constraint dname_nn not null);
```

## 무결성 제약 조건 비활성화

```sql
ALTER TABLE SUGANG
DISABLE CONSTRAINT SUGANG_PK;

ALTER TABLE SUGANG
DISABLE CONSTRAINT SUGANG_STUDNO_FK;

SELECT CONSTRAINT_NAME, STATUS
FROM USER_CONSTRAINTS
WHERE TABLE_NAME IN('SUGANG', 'SUBJECT');
```

## 무결성 제약 조건 활성화

```sql
ALTER TABLE SUGANG
ENABLE CONSTRAINT SUGANG_PK;

ALTER TABLE SUGANG
ENABLE CONSTRAINTS SUFANG_SYUDNO_FK;

SELECT CONSTRAINT_NAME, STATUS
FROM USER_CONSTRAINTS
WHERE TABLE_NAME = 'SUGANG';
```

## 무결성 제약 조건 조회

```sql
SELECT table_name, constraint_name, constraint_type, status
from user_constraints
where table_name in ('STUDNO', 'PROFESSOR', 'DEPARTMENT');
```

# 인덱스 관리

## 고유 인덱스 생성

```sql
CREATE UNIQUE INDEX idx_dept_name
ON department(dname);
```

## 비고유 인덱스

```sql
CREATE INDEX idx_stud_birthdate
ON student(birthdate);
```

## 단일 인덱스, 결합 인덱스

- 단일 인덱스: 하나의 컬럼으로 구성된 인덱스
- 결합 인덱스: 두 개 이상의 컬럼으로 구성된 인덱스

```sql
CREATE INDEX idx_stud_dno_grade
ON student(deptno, grade);
```

## DESCENDING INDEX

```sql
CREATE INDEX fidx_stud_no_name ON student(deptno DESC, 
name ASC);
```

## 함수 기반 인덱스

```sql
CREATE INDEX UPPERCASE_IDX ON EMP (UPPER(ename));
SELECT * FROM EMP WHERE UPPER(ENAME) = 'KING';

# 함수기반 인덱스(FBI)
CREATE INDEX   idx_standard_weight ON STUDENT((HEIGHT-100) * 0.9);
```

## 인덱스 실행 경로

```sql
# plustrace 롤을 scott 사용자에게 부여
grant plustrace to scott;

# 모든 SQL 명령문에 대해 실행 경로를 출력하기 위한 환경 설정
SET AUTOTRACE ON;

#  SQL DEVELOPER에서 F10 눌러도 가능
```

## 인덱스 정보 조회

```sql
# USER_INDEXES
SELECT index_name, uniqueness
FROM user_indexes
WHERE table_name = 'STUDENT';

# USER_IND_COLUMNS
SELECT index_name, column_name
FROM user_ind_columns
WHERE TABLE_NAME = 'STUDENT';
```

# VIEW

- 데이터 딕셔너리 테이블에 뷰에 대한 정의만 저장
- 디스크 저장공간 할당 안됨
- 장점
    - 데이터를 보호하기 위한 보안(Security)
    - 사용자 편의성(Flexibility)
- 굳이 JOIN을 하지 않고, 만들어진 VIEW를 통해 빠른 조회 가능

## VIEW 생성

```sql
# VIEWS 생성
CREATE VIEW VIEW_PROFESSOR AS
SELECT profno, name, userid, position, hiredate, deptno
FROM professor;

# 생성한 VIEW 조회
SELECT * FROM VIEW_PROFESSOR;
```

## 단순 VIEW 생성

```sql
# 단순 VIEW 생성
CREATE view v_stud_dept101 as 
        SELECT STUDNO, NAME, DEPTNO
        FROM STUDENT
        WHERE DEPTNO = 101;

# 조회
SELECT * FROM v_stud_dept101;
```

## 복합 VIEW 생성

```sql
# 복합 VIEW 생성
CREATE view v_stud_dept102
AS SELECT s.studno, s.name, s.grade, d.dname
FROM student s, department d
WHERE s.deptno = d.deptno and s.deptno=102;

# 조회
SELECT * FROM v_stud_dept102;
```

## 함수 사용 VIEW 생성

```sql
# 별명을 설정해 주어야 실행 가능
CREATE view v_prof_avg_sal
AS SELECT deptno, sum(sal) sum_sal, avg(sal) avg_sal
FROM professor
GROUP BY deptno;

# 조회
SELECT * FROM v_prof_avg_sal;
```

## 인라인 VIEW

```sql
SELECT dname, avg_height, avg_weight
FROM (SELECT deptno, avg(height)avg_height, avg(weight) avg_weight
        FROM student
        GROUP BY deptno) s, department d
WHERE s.deptno = d.deptno;

# 각 학년의 평균 키를 구하고 평균 키보다 큰 학생의 학년, 이름, 키, 각 학년의 평균 키 출력

SELECT s.grade, s.name, s.height, a.avg_height
FROM (SELECT grade, ROUND(AVG(height)) avg_height
        FROM student
        GROUP BY grade) a, student s
WHERE a.grade = s.grade;

```

## VIEW 조회

- 사용자가 생성한 모둔 VIEW에 대한 정의 저장

```sql
SELECT view_name, text
FROM user_views;
```

## VIEW 변경

```sql
CREATE OR REPLACE VIEW V_STUD_DEFT101
AS SELECT studno, name, deptno, grade
    FROM student
    WHERE deptno = 101;
```

## VIEW 삭제

```sql
DROP VIEW v_stud_dept101;
```

# 사용자 권한 제어

## 시스템 권한 - 데이터베이스 관리자

| CREATE USER | 사용자를 생성할 수 있는 권한 |
| --- | --- |
| DROP USER | 사용자를 삭제할 수 있는 권한 |
| DROP ANY TABLE | 임의의 테이블을 삭제할 수 있는 권한 |
| QUERY REWRITE | 함수 기반 인덱스를 생성하기 위한 권한 |
| BACKUP ANY TABLE | 익스포트 유틸리티를 사용해서 임의의 테이블을 백업할 수 있는 권한 |

## 시스템 권한 - 일반 사용자

| CREATE SESSION | 데이터베이스에 접속할 수 있는 권한 |
| --- | --- |
| CREATE TABLE | 사용자 스키마에서 테이블을 생성할 수 있는 권한 |
| CREATE SEQUENCE | 사용자 스키마에서 시퀀스를 생성할 수 있는 권한 |
| CREATE VIEW | 사용자 스키마에서 뷰를 생성할 수 있는 권한 |
| CREATE PROCEDURE | 사용자 스키마에서 프로시저, 함수, 패키지를 생성할 수 있는 권한 |

## 시스템 권한 부여

```sql

conn system/manager
grant query rewrite to scott; # 권한부여
grant query rewrite to public; # 권한부여
conn scott/tiger
SELECT * FROM user_sys_privs; # 권한 조회
```

## 권한 조회

```sql
SELECT * FROM session_privs;
```

## 시스템 권한 철회

```sql
REVOKE query rewrite FROM scott;
```

## 객체 권한

| 객체 권한 종류 | 테이블 | 뷰 | 시퀀스 | 프로시져 |
| --- | --- | --- | --- | --- |
| ALTER | O |  | O |  |
| DELETE | O | O |  | O |
| EXECUTE |  |  |  |  |
| INDEX | O |  |  |  |
| INSERT | O | O |  |  |
| REFERENCES | O |  |  |  |
| SELECT | O | O | O |  |
| UPDATE | O | O |  |  |

## 에러 해결

```sql
ORA-01031: insufficient privileges

conn system/manager 입력 후, 진행

# 테이블이나 뷰가 존재하지 않는 경우
# 테이블 또는 뷰 이름 확인 해결
# 권한 부여 확인을 통해 해결
ERROR at line 1: ORA-00942: table or view does not exist

```

# 문제 연습

```sql
1. 
SQL> CREATE TABLESPACE SESAC
  2  DATAFILE 'C:\oraclexe\app\oracle\oradata\XE\sesac.dbf' size 100m;

Tablespace created.

2.
SQL> CREATE USER SESAC IDENTIFIED BY SESAC20
  2  DEFAULT TABLESPACE SESAC
  3  TEMPORARY TABLESPACE TEMP;

User created.

SQL> grant connect, resource to SESAC;

3.
SQL> create table s_order(
  2  id number(7)
  3     constraint order_id_pk primary key,
  4  customer_id number(7)
  5     constraint order_cd_nn NOT NULL,
  6  data_ord date,
  7  total number(11,2),
  8  payment_type varchar2(6)
  9     constraint order_pay_ck check(payment_type in('현금', '카드')));

Table created.

4. 
SQL> INSERT INTO s_order
  2  VALUES(100, 204, '18/08/07', 6110000, '카드');

1 row created.

SQL> INSERT INTO s_order
  2  VALUES(102, 206, '18/09/07', 1865600, '카드');

1 row created.

SQL> INSERT INTO s_order
  2  VALUES(103, 206, '18/10/07', 1865600, '현금');

1 row created.

5.
SQL> UPDATE s_order
  2  SET payment_type = '카드'
  3  WHERE id = 103;

1 row updated.

6. 

```

# study

```sql
comment on table address
is '고객 주소록을 관리하기 위한 테이블';

SELECT COMMENTS
FROM user_tab_comments
where table_name = 'ADDRESS';

COMMENT ON TABLE ADDRESS 
IS '';
COMMENT ON COLUMN ADDRESS.NAME is '';

COMMENT ON COLUMN ADDRESS.NAME is '';

SELECT COMMENTS
FROM USER_TAB_COMMENTS
WHERE TABLE_NAME = 'ADDRESS';

SELECT TABLE_NAME FROM USER_TABLES;

SELECT OWNER, TABLE_NAME FROM ALL_TABLES;

SELECT OWNER, TABLE_NAME FROM DBA_TABLES;

CREATE TABLE SUBJECT
(SUBNO NUMBER(5)
    CONSTRAINT SUBJECT_NO_PK PRIMARY KEY
    DEFERRABLE INITIALLY DEFERRED
    USING INDEX TABLESPACE INDX,
SUBNAME VARCHAR2(20)
    CONSTRAINT SUBJECT_NAME_NN NOT NULL,
TERM VARCHAR(1)
    CONSTRAINT SUBJECT_TERM_CK CHECK (TERM IN ('1', '2')),
    RTPE VARCHAR2(1));
    
AlTER TABLE STUDENT
ADD CONSTRAINT STUD_NO_PK PRIMARY KEY(STUDNO);

CREATE TABLE SUGANG
(STUDNO NUMBER(5)
    CONSTRAINT SUGANG_STUDNO_FK REFERENCES STUDENT(STUDNO),
SUBNO NUMBER(5)
    CONSTRAINT SUGANG_STUDNO_FK REFERENCES SUBJECT(SUBNO),
REGDATE DATE,
RESULT NUMBER(3),
    CONSTRAINT SUGANG_PK PRIMARY KEY(STUDNO, SUBNO));

SELECT CONSTRAINT_NAME, CONSTRAINT_TYPE
FROM USER_CONSTRAINTS
WHERE TABLE_NAME IN ('SUBJECT', 'SUGANG', 'STUDENT');

ALTER TABLE STUDENT
ADD CONSTRAINT STUD_IDNUM_UK UNIQUE(IDNUM);

ALTER TABLE STUDENT
MODIFY (NAME CONSTRAINT STUD_NAME_NN NOT NULL);
SELECT * FROM DEPARTMENT;

DELETE from department 
WHERE DEPTNO = 301;

ALTER TABLE DEPARTMENT
ADD CONSTRAINT DEPT_PK PRIMARY KEY(DEPTNO);

ALTER TABLE STUDENT ADD CONSTRAINT STUD_DEPTNO_FK
FOREIGN KEY(DEPTNO) REFERENCES DEPARTMENT(DEPTNO);

alter table department
modify (dname constraint dname_nn not null);

INSERT INTO DEPARTMENT(DEPTNO, DNAME)
VALUES(301, '생명공학과');

INSERT INTO DEPARTMENT(DEPTNO, DNAME)
VALUES(301, '생명공학과');

ALTER TABLE PROFESSOR ADD CONSTRAINT PRF_PK PRIMARY KEY(PROFNO);
ALTER TABLE PROFESSOR MODIFY (NAME NOT NULL);
ALTER TABLE PROFESSOR ADD CONSTRAINTS PROF_PK
FOREIGN KEY(DEPTNO) REFERENCES DEPARTMENT(DEPTNO);

INSERT INTO subject values(1, 'sql', '1' , '필수');

INSERT INTO subject values(2, '', '2', '필수');

ALTER TABLE SUGANG
DISABLE CONSTRAINT SUGANG_PK;

ALTER TABLE SUGANG
DISABLE CONSTRAINT SUGANG_STUDNO_FK;

SELECT CONSTRAINT_NAME, STATUS
FROM USER_CONSTRAINTS
WHERE TABLE_NAME IN('SUGANG', 'SUBJECT');

alter table subject
drop constraint subject_term_ck;

ALTER TABLE SUGANG
ENABLE CONSTRAINT SUGANG_PK;

ALTER TABLE SUGANG
ENABLE CONSTRAINTS SUFANG_SYUDNO_FK;

SELECT CONSTRAINT_NAME, STATUS
FROM USER_CONSTRAINTS
WHERE TABLE_NAME = 'SUGANG';

SELECT table_name, constraint_name, constraint_type, status
from user_constraints
where table_name in ('STUDNO', 'PROFESSOR', 'DEPARTMENT');

CREATE UNIQUE INDEX idx_dept_name
ON department(dname);

CREATE INDEX idx_stud_birthdate
ON student(birthdate);

CREATE INDEX idx_stud_dno_grade
ON student(deptno, grade);

CREATE INDEX fidx_stud_no_name ON student(deptno DESC, 
name ASC);

CREATE INDEX UPPERCASE_IDX ON EMP (UPPER(ename));
SELECT * FROM EMP WHERE UPPER(ENAME) = 'KING';

CREATE INDEX   idx_standard_weight ON STUDENT((HEIGHT-100) * 0.9);

SET AUTOTRACE ON;

SELECT * FROM DEPARTMENT;

SELECT DEPTNO, DNAME
FROM DEPARTMENT
WHERE DNAME = '정보미디어학부';

DROP INDEX IDX_DEPT_NAME;

drop index idx_stud_birthdate;

set autot off;

SELECT index_name, uniqueness
FROM user_indexes
WHERE table_name = 'STUDENT';

SELECT index_name, column_name
FROM user_ind_columns
WHERE TABLE_NAME = 'STUDENT';

DROP INDEX fidx_stud_no_name;

ALTER INDEX stud_no_pk REBULID;

CREATE VIEW VIEW_PROFESSOR AS
SELECT profno, name, userid, position, hiredate, deptno
FROM professor;

SELECT * FROM VIEW_PROFESSOR;

CREATE view v_stud_dept101 as 
        SELECT STUDNO, NAME, DEPTNO
        FROM STUDENT
        WHERE DEPTNO = 101;
        
SELECT * FROM v_stud_dept101;

CREATE view v_stud_dept102
AS SELECT s.studno, s.name, s.grade, d.dname
FROM student s, department d
WHERE s.deptno = d.deptno and s.deptno=102;

SELECT * FROM v_stud_dept102;

CREATE view v_prof_avg_sal
AS SELECT deptno, sum(sal) sum_sal, avg(sal) avg_sal
FROM professor
GROUP BY deptno;

SELECT * FROM v_prof_avg_sal;

SELECT dname, avg_height, avg_weight
FROM (SELECT deptno, avg(height)avg_height, avg(weight) avg_weight
        FROM student
        GROUP BY deptno) s, department d
WHERE s.deptno = d.deptno;

SELECT * FROM student;
SELECT * FROM department;

SELECT s.grade, s.name, s.height, a.avg_height
FROM (SELECT grade, ROUND(AVG(height)) avg_height
        FROM student
        GROUP BY grade) a, student s
WHERE a.grade = s.grade;

SELECT view_name, text
FROM user_views;

CREATE OR REPLACE VIEW V_STUD_DEFT101
AS SELECT studno, name, deptno, grade
    FROM student
    WHERE deptno = 101;
    
DROP VIEW v_stud_dept101;
DROP VIEW v_stud_dept102;

connect system/manager;

SELECT * FROM session_privs;

REVOKE query rewrite FROM scott;

CREATE USER tiger IDENTIFIED BY tiger123
DEFAULT TABLESPACE USERS
TEMPORARY TABLESPACE temp;
```