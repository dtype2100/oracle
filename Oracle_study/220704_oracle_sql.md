# 서브쿼리

```sql
SELECT NAME, POSITION 
FROM PROFESSOR
WHERE POSITION = (SELECT POSITION FROM PROFESSOR WHERE NAME = '전은지');

# jun123과 같은 학년의 학생도 함께 조회 가능
SELECT STUDNO, NAME, GRADE
FROM STUDENT
WHERE GRADE = (SELECT GRADE
                        FROM STUDENT
                        WHERE USERID = 'jun123');

# STUDNO 20101과 같은 학년이고, 키가 큰 학생 출력
SELECT NAME, GRADE, HEIGHT
FROM STUDENT
WHERE GRADE = (SELECT GRADE
                FROM STUDENT
                WHERE STUDNO = 20101)
AND HEIGHT > (SELECT HEIGHT
                FROM STUDENT
                WHERE STUDNO = 20101);

# STUDNO 20101과 같은 학년이고, 키가 큰 학생 출력, 학과 추가
SELECT S.NAME, S.GRADE, S.HEIGHT, S.STUDNO, D.DNAME
FROM STUDENT S 
	NATURAL JOIN DEPARTMENT D
WHERE GRADE = (SELECT GRADE
                FROM STUDENT
                WHERE STUDNO = 20101)
AND HEIGHT > (SELECT HEIGHT
                FROM STUDENT
                WHERE STUDNO = 20101);         
```



# 서브쿼리 다중행 연산자

* IN: 메인 쿼리의 비교 조건이 서브쿼리의 결과 중에서 일치하면 참. '='비교만 가능
* ANY, SOME: 메인 쿼리의 비교 조건이 서브쿼리의 결과 중에서 하나라도 일치하면 참. '=' 비교만 가능.
* ALL: 메인 쿼리의 비교 조건이 서브퀄이의 결과중에서 모든 값이 일치하면 참.
* EXISTS: 메인 쿼리의 비교 조건이 서브쿼리의 결과중에서 만족하는 값이 하나라도 존재하면 참

## IN

```SQL
SELECT NAME, GRADE, DEPTNO
FROM STUDENT
WHERE DEPTNO IN (SELECT DEPTNO
                    FROM DEPARTMENT
                    WHERE COLLEGE = 100);
```



## ANY

* ANY는 MIN 을 사용한 것과 같음

```SQL
# ANY 사용 연산자 사용
SELECT STUDNO, NAME, HEIGHT
FROM STUDENT
WHERE HEIGHT > ANY (SELECT HEIGHT
                    FROM STUDENT
                    WHERE GRADE = '4');

# MIN 사용 연산자 사용
SELECT STUDNO, NAME, HEIGHT
FROM STUDENT
WHERE HEIGHT > (SELECT MIN(HEIGHT)
                FROM STUDENT
                WHERE GRADE = '4');
```



## ALL

* ALL은 MAX를 사용하는 것과 같음

```SQL
# ALL
SELECT STUDNO, NAME, HEIGHT
FROM STUDENT
WHERE HEIGHT > ALL (SELECT HEIGHT
                    FROM STUDENT
                    WHERE GRADE = '4');

# MAX
SELECT STUDNO, NAME, HEIGHT
FROM STUDENT
WHERE HEIGHT > (SELECT MAX(HEIGHT)
                FROM STUDENT
                WHERE GRADE = '4');
```



## EXISTS

* 하나라도 존재하면 참.
* 대량의 데이터베이스가 있는 곳에서 많이 사용
* 결과가 하나라도 있으면 출력 가능하지만, 없으면 "선택된 레코드가 없다"고 나옴

```SQL
# EXISTS 연산자 사용
SELECT PROFNO, NAME, SAL, COMM, SAL+NVL(COMM, 0)
FROM PROFESSOR
WHERE EXISTS (SELECT PROFNO
                FROM PROFESSOR
                WHERE COMM IS NOT NULL);
                
# NOT EXISTS 연산자 사용
# 존재하지 않으면 1 출력
SELECT 1 USERID_EXIST
FROM DUAL
WHERE NOT EXISTS (SELECT USERID
                    FROM STUDENT
                    WHERE USERID = 'GOODSTUDENT');

# 전은지가 없는지 확인
# 있으면 결과 출력되지 않음
# NOT EXISTS를 사용하기 때문
SELECT NAME, POSITION 
FROM PROFESSOR
WHERE NOT EXISTS(SELECT * 
                 FROM PROFESSOR 
                 WHERE NAME = '전은지');
```



# 연습

```SQL
# 전체 학과번호 수, 전체 학생 수 출력
SELECT DEPTNO, COUNT(*) "학생수"
FROM STUDENT
GROUP BY DEPTNO
HAVING COUNT(STUDNO) = (SELECT MAX(COUNT(STUDNO))
                        FROM STUDENT
                        GROUP BY DEPTNO);

```



# 다중 컬럼 서브쿼리

* 서브쿼리에서 여러 컬럼 값을 검색하여 메인쿼리의 조건절과 비교하는 서브쿼리

* PAIRWISE:  컬럼을 쌍으로 묶어서 동시에 비교하는 방식
* UNPAIRWISE:  컬럼별로 나누어서 비교한 후, AND 연산하는 방식

## PAIRWISE

```SQL
# PAIRWISE
SELECT NAME, GRADE, WEIGHT
FROM STUDENT
WHERE (GRADE, WEIGHT) IN (SELECT GRADE, MIN(WEIGHT)
                            FROM STUDENT
                            GROUP BY GRADE);
                            
# 각 부서별로 가장 높은 급여를 받는 직원의 부서번호, 이름, 급여 출력   
SELECT DEPTNO, ENAME, SAL
 FROM EMP
 WHERE (DEPTNO, SAL) IN (SELECT DEPTNO, MAX(SAL)
                         FROM EMP
                         GROUP BY DEPTNO);
```

## UNPAIRWISE

```SQL
SELECT NAME, GRADE, WEIGHT
FROM STUDENT
WHERE GRADE IN (SELECT GRADE
                FROM STUDENT
                GROUP BY GRADE)
AND WEIGHT IN (SELECT MIN(WEIGHT)
                FROM STUDENT
                GROUP BY GRADE);
```



# 상호연관 서브쿼리

* 성능이 저하될 수 있음
* 메인에 S1이 있는데, 서브에도 S1사용
* 대용량 데이터에 성능이 저하될 수 있음

```SQL
SELECT NAME, DEPTNO, HEIGHT
FROM STUDENT S1
WHERE HEIGHT > (SELECT AVG(HEIGHT)
                FROM STUDENT S2
                WHERE S2.DEPTNO = S1.DEPTNO);
```



# 실무에서 서브쿼리 사용시 주의사항

1. 서브쿼리 내에서 ORDER BY 사용하면 오류 발생



# 실습

```SQL
SELECT ENAME, HIREDATE
FROM EMP
WHERE DEPTNO = (SELECT DEPTNO
                FROM EMP
                WHERE INITCAP(ENAME) = 'Blake');
                
SELECT EMPNO, ENAME, SAL
FROM EMP
WHERE SAL > (SELECT AVG(SAL)
             FROM EMP)
ORDER BY SAL DESC;

SELECT ENAME, DEPTNO, SAL
FROM EMP
WHERE (DEPTNO, SAL) IN(SELECT DEPTNO, SAL
                        FROM EMP
                        WHERE COMM IS NOT NULL);
```



# Scalar Subquery

* 소량의 데이터에서 효과적
* 대량의 데이터에서 성능 저하 가능

```sql
```



# 데이터 조작어

* 데이터 조작하기 위한 명령어
* 



# INSERT

```SQL
DESC STUDENT;
INSERT INTO STUDENT
VALUES(1010, '홍길동', 'hong', '1', '8501011143098', ' 85/01/01', '041)3.0-3114',
        170, 70, 101, 9903);

# 묵시적인 방법
INSERT INTO DEPARTMENT(DEPTNO, DNAME)
VALUES(3000, '생명공학부');

# 명시적인 방법
# 나머지 컬럼에 NULL 입력
INSERT INTO DEPARTMENT
VALUES(301, '환경보건학과', '', '');

# 날짜 형식 입력하는 예
INSERT INTO PROFESSOR(PROFNO, NAME, POSITION, HIREDATE, DEPTNO)
VALUES(9920, '최윤식', '조교수',
        TO_DATE('2006/01/01', 'YYYY/MM/DD'), 102);

# SYSDATE 함수를 이용한 현재 날짜 입력
INSERT INTO PROFESSOR
VALUES (9910, '백미선', 'WHITE', '전임강사', 200, SYSDATE, 0, 101);


```

## 단일 테이블에 다중 행 입력

```SQL
# 테이블 생성
# 테이블 내용은 제외하고 테이블의 구조만 가져오는 방식
# 'WHERE 1=0'이라는 조건이 없기 때문
# 프로그램 테스트용으로 사용 가능
CREATE TABLE T_STUDENT
AS SELECT * FROM STUDENT
WHERE 1=0; # 거짓 조건

# T_STUDENT 테이블에 STUDENT 테이블 삽입
INSERT INTO T_STUDENT
SELECT * FROM STUDENT;
```



## INSERT ALL

* 보안이 필요한 경우, 필요한 데이터만 선택해서 저장 가능

```SQL
# 테이블 생성
CREATE TABLE HEIGHT_INFO(
STUDNO NUMBER(5),
NAME VARCHAR2(10),
HEIGHT NUMBER(5, 2));

SELECT * FROM HEIGHT_INFO;

CREATE TABLE WEIGHT_INFO(
STUDNO NUMBER(5),
NAME VARCHAR2(10),
HEIGHT NUMBER(5, 2));

SELECT * FROM WEIGHT_INFO;

# INSERT ALL
INSERT ALL
INTO HEIGHT_INFO VALUES (STUDNO, NAME, HEIGHT)
INTO WEIGHT_INFO VALUES (STUDNO, NAME, WEIGHT)
SELECT STUDNO, NAME, HEIGHT, WEIGHT
FROM STUDENT
WHERE GRADE >= '2';

SELECT * FROM HEIGHT_INFO;
SELECT * FROM WEIGHT_INFO;
SELECT * FROM HEIGHT_INFO, WEIGHT_INFO;
```



# DELETE

```SQL
# DELETE FROM 테이블 명
DELETE FROM HEIGHT_INFO;
COMMIT;
```



# Conditional INSERT ALL

* 지정한 조건을 만족하는 행을 해당 테이블에 각각 입력
* ALL:  WHEN~THEN~ELSE의 조건을 만족하는 서브쿼리의 모든 검색결과 입력을 위한 옵션
* WHEN 조건절 THEN: 서브쿼리의 결과 집합에 대한 비교 조건

```SQL
INSERT ALL
WHEN HEIGHT > 170 THEN
    INTO HEIGHT_INFO VALUES(STUDNO, NAME, HEIGHT)
WHEN WEIGHT > 70 THEN
    INTO WEIGHT_INFO VALUES(STUDNO, NAME, WEIGHT)
SELECT STUDNO, NAME, HEIGHT, WEIGHT
FROM STUDENT
WHERE GRADE >= '2';

SELECT * FROM HEIGHT_INFO;
SELECT * FROM WEIGHT_INFO;

```



# Conditional-First INSERT

* 첫 번째 테이블 우선 입력
* 서브쿼리 결과 중 조건을 만족하는 첫 WHEN절에서 지정한 테이블 입력

```SQL
INSERT FIRST
WHEN HEIGHT > 170 THEN
    INTO HEIGHT_INFO VALUES(STUDNO, NAME, HEIGHT)
WHEN WEIGHT > 70 THEN
    INTO WEIGHT_INFO VALUES(STUDNO, NAME, WEIGHT)
SELECT STUDNO, NAME, HEIGHT, WEIGHT
FROM STUDENT
WHERE GRADE >= '2';

SELECT * FROM WEIGHT_INFO;
SELECT * FROM HEIGHT_INFO;

```



# PIVOTING

```sql
CREATE TABLE SALES (
SALES_NO NUMBER(4),
WEEK_NO NUMBER(2),
SALES_MON NUMBER(7,2),
SALES_TUE NUMBER(7,2),
SALES_WEB NUMBER(7,2),
SALES_THU NUMBER(7,2),
SALES_FRI NUMBER(7,2));

SELECT * FROM SALES;

INSERT INTO SALES VALUES(1102, 4, 100, 150, 80, 60, 120);

CREATE TABLE SALES_DATA (
SALE_NO NUMBER(4),
WEEK_NO NUMBER(2),
DAY_NO NUMBER(2),
SALES NUMBER(7, 2));

SELECT * FROM SALES_DATA;

INSERT ALL
INTO SALES_DATA VALUES(SALES_NO, WEEK_NO, '1', SALES_MON)
INTO SALES_DATA VALUES(SALES_NO, WEEK_NO, '2', SALES_TUE)
INTO SALES_DATA VALUES(SALES_NO, WEEK_NO, '3', SALES_WEB)
INTO SALES_DATA VALUES(SALES_NO, WEEK_NO, '4', SALES_THU)
INTO SALES_DATA VALUES(SALES_NO, WEEK_NO, '5', SALES_FRI)
SELECT SALES_NO, WEEK_NO, SALES_MON, SALES_TUE, SALES_WEB,
        SALES_THU, SALES_FRI
FROM SALES;
```



# ROLLBACK

* 커밋 전까지 ROLLBACK 가능



# 데이터 수정

```SQL
SELECT PROFNO, NAME, POSITION
FROM PROFESSOR
WHERE PROFNO = 9903;

UPDATE PROFESSOR
SET POSITION = '부교수'
WHERE PROFNO = '9903';

SELECT * FROM PROFESSOR;

ROLLBACK;
```

```SQL

SELECT STUDNO, GRADE, DEPTNO
FROM STUDENT
WHERE STUDNO = 10201;

SELECT STUDNO, GRADE, DEPTNO
FROM STUDENT
WHERE STUDNO = 10103;
```

## 서브쿼리를 이용한 데이터 수정

```SQL
UPDATE STUDENT
SET (GRADE, DEPTNO) = (SELECT GRADE, DEPTNO
                        FROM STUDENT
                        WHERE STUDNO = 10103)
WHERE STUDNO = 10201;
```



# 데이터 수정 연습

* 교수 테이블에서 이만식 교수의 직급과 동일 직급을 가진 교수 중 현재 급여가 450 미만인 교수 급여 12% 인상

```SQL
UPDATE PROFESSOR
SET    SAL = SAL * 1.2
WHERE  POSITION = (SELECT POSITION
                   FROM PROFESSOR
                   WHERE NAME = '이만식')
AND SAL < 450;

SELECT * FROM PROFESSOR;
```



# 데이터 삭제

* 데이터가 너무 많은 경우, DELETE의 주기를 짧게해야 서버 끊김 방지 가능



# 단일 행 삭제

```SQL
DELETE FROM STUDENT
WHERE STUDNO = 20103;
```



# 서브쿼리를 이용한 데이터 삭제

```SQL
DELETE FROM EMP
WHERE DEPTNO = (SELECT DEPTNO 
                FROM DEPT
                WHERE LOC = 'CHICAGO');
```



# MERGR

```SQL
# PROFESSOR 테이블에서 직급이 교수인 데이터 저장
CREATE TABLE PROFESSOR_TEMP AS
SELECT *
FROM PROFESSOR
WHERE POSITION = '교수';

# 교수인 직급을 명예교수로 수정
UPDATE PROFESSOR_TEMP
SET POSITION = '명예교수'
WHERE POSITION = '교수';

# 새로운 데이터 입력
INSERT INTO PROFESSOR_TEMP
VALUES(9999, '김도경', 'ARO21', '전임강사', 200, SYSDATE, 10, 101);

# MERGE
MERGE INTO PROFESSOR P
USING PROFESSOR_TEMP F
ON (P.PROFNO = F.PROFNO)
WHEN MATCHED THEN
UPDATE SET P.POSITION = F.POSITION
WHEN NOT MATCHED THEN
INSERT VALUES(F.PROFNO, F.NAME, F.USERID, F.POSITION, F.SAL, F.HIREDATE, F.COMM, F.DEPTNO);

SELECT * FROM PROFESSOR;
```



# sequence

```sql
# SEQUENCR 생성
CREATE SEQUENCE S_SEQ
INCREMENT BY 1
START WITH 1
MAXVALUE 100;
  
# SEQUENCE 확인  
SELECT min_value, max_value, increment_by, last_number
FROM USER_SEQUENCES
WHERE SEQUENCE_NAME = 'S_SEQ';
```

```sql
INSERT INTO EMP_VALUES
VALUES(S_SEQ.NEXTVAL, 'TEST1', 'SALESMAN', 7698, to_date('17-12-1980','dd-mm-yyyy'), 800, NULL,20);

INSERT INTO(EMPNO, ENAME)
VALUES(S_SEQ,NEXTVAL, 'TEST2');

INSERT INTO EMP(EMPNO, ENAME)
VALUES(SEO.NEXTVAL, 'TEST3');

```



# sequence 변경

* 시퀀스 최대값 200으로 변경

```sql
ALTER SEQUENCE  s_seq maxvalue 200;
```



# sequence 삭제

```sql
 DROP sequence S_SEQ;
```



# 서브쿼리를 이용한 테이블 생성

```SQL
CREATE TABLE ADDR_SECOND(ID, NAME, ADDR, PHON, E_MAIL)
AS SELECT * FROM ADDRESS;
```



# 테이블 구조 복사

```SQL
CREATE TABLE ADDR_FOURTH
AS SELECT ID, NAME FROM ADDRESS
WHERE 1=2;
```



# 테이블 컬럼 추가

```SQL
ALTER TABLE ADDRESS
ADD (BIRTH, DATE);
```



# 테이블 컬럼 삭제

```SQL
ALTER TABLE 테이블 명 DROP 컬럼;
```



# 테이블 컬럼 변경

```sql
ALTER TABLE ADDRESS
MODIFY PHON VARCHAR2(50);
```



# 테이블 이름 변경

```sql
RENAME old_table TO new_table;
RENAME ADDR_SECOND TO CLIENT_ADDRESS;
```



# 테이블 삭제

```SQL
DROP TABLE ADDR_THIRD;
```



# TRUNCATE

* 테이블의 데이터와 할당된 공간만 삭제

```SQL
TRUNCATE TABLE CLIENT_ADDRESS;
```



# DELETE와 TRUNCATE 차이

* DELETTE
  * 기존 데이터만 삭제
  * ROLLBACK 가능
  * WHERE 절을 이용하여 특정 행만 삭제 가능
* TRUNCATE
  * 기존 데이터 삭제
  * 물리적인 공간까지 반환
  * DDL문으로 ROLLBACK 불가능
  * WHERE절을 이용하여 특정 행만 삭제 불가능



# DELETE, DROP, TRNCATE 비교

* DELETE
  * 테이블 정의: 존재
  * 저장공간: 유지
  * 데이터: 삭제
  * 작업속도: 느림
  * SQL문 종류: DDL
* DROP
  * 테이블 정의: 존재
  * 저장공간: 반납
  * 데이터: 삭제
  * 작업속도: 빠름
  * SQL문 종류: DDL
* TRUNCATE
  * 테이블 정의: 삭제
  * 저장공간: 반납
  * 데이터: 삭제
  * 작업속도: 빠름
  * SQL문 종류: DDL



# 연습문제

```sql
1.
CREATE TABLE prof1
(profno NUMBER(4),
 name   VARCHAR2(10));

CREATE TABLE prof2
(profno NUMBER(4),
 name   VARCHAR2(10)); 

2.
 INSERT ALL
WHEN profno BETWEEN 9906 and 9905 THEN INTO prof1 VALUES (profno, name)
SELECT profno, name
FROM prof1;

INSERT ALL
WHEN profno BETWEEN 9906 and 9920 THEN INTO prof2 VALUES (profno, name)
SELECT profno, name
FROM prof1;

3.
SELECT p.profno, p.name, p.hiredate, d.dname
FROM professor p
        NATURAL JOIN department d
WHERE hiredate IN (SELECT MIN(hiredate) FROM professor
                    GROUP BY deptno)
ORDER BY p.hiredate;

4.
SELECT * FROM STUDENT;
DELETE FROM STUDENT
WHERE STUDNO BETWEEN 20000 AND 25000;

5.
CREATE TABLE EMPLOYEE2(ID, LAST_NAME, DEPT_ID)
AS SELECT EMPNO, ENAME, DEPTNO FROM EMP;

6. ALTER TABLE employee2 MODIFY last_name VARCHAR2(30);

```



