# HAVING

```sql
# WHERE 조건에 맞는 값에서 그룹에 대한 조건을 찾을 때, HAVING 사용
# GROUP의 조건에 맞는 값은 HAVING을 사용
# WHERE 절에서 GROUP 함수 사용 X.

# 확인 코드
SELECT grade, COUNT(*), ROUND(AVG(height)) avg_height,
        ROUND(AVG(weight)) avg_weight
FROM student
GROUP BY grade
ORDER BY avg_height DESC;
-------------------------------
4	3	176	85
1	6	175	65
3	2	171	79
2	5	165	53

# HAVING 적용
SELECT grade, COUNT(*), ROUND(AVG(height)) avg_height,
        ROUND(AVG(weight)) avg_weight
FROM student
GROUP BY grade
HAVING COUNT(*) > 4
ORDER BY avg_height DESC;
-------------------------------
1	6	175	65
2	5	165	53

SELECT deptno, ROUND(AVG(sal), 1) 
FROM emp
GROUP BY deptno
HAVING AVG(sal) >= 1900;

SELECT deptno, ROUND(AVG(sal), 1) 
FROM emp
WHERE sal >= 1000
GROUP BY deptno
HAVING AVG(sal) >= 1900;

# 함수 중첩은 가장 안쪽에서 가장 바깥쪽 순서로 확인.
SELECT deptno, AVG(weight)
FROM student
GROUP BY deptno;

SELECT MAX(AVG(WEIGHT)) max_weight
FROM student
GROUP BY deptno;

SELECT MAX(COUNT(studno)) max_cnt, MIN(COUNT(studno)) min_cnt
FROM student
GROUP BY deptno;
```



# HAVING과 WHERE 성능 차이

## WHERE가 HAVING보다 성능이 좋은 이유

1. 먼저 GROUP BY를 하고 HAVING으로 조건을 설정하면, 불필요한 데이터도 그룹 안에 포함
2. 반면, 먼저 WHERE로 조건을 설정하고 GROUP BY를 하면, 미리 불필요한 데이터를 제외하고 그룹을 만들 수 있음

3. 즉, 먼저 불필요한 데이터를 제외하고 그룹을 만들면, 필요한 데이터만 그룹을 만들 수 있기 때문에 HAViNG보다 WHERE가 더 효율적

## 코드 예제

```sql
# 행 집합을 GROUP절로 정렬 후, HAVING절을 적용하면 비효율
# GROUP BY를 통해 정렬하고, 그룹한 절에 대해 HAVING을 통해 조건 설정
# 선 그룹, 후 조건
SELECT deptno, AVG(sal)
FROM professor
GROUP BY deptno
HAVING deptno > 102;

# 그룹화에 불필요한 절을 WHERE절로 제외 후, GROUP BY절을 실행하여 내부 정렬에 필요한 행의 수를 줄여서 효율적. 
# WHERE절로 먼저 조건을 설정, 이후 GROUP BY로 그룹만 생성
# 선 조건, 후 그룹
SELECT deptno, AVG(sal)
FROM professor
WHERE deptno > 102
GROUP BY deptno;
```

## 그럼에도 HAVING이 필요한 이유

```sql
# WHERE절로 조건을 설정하고 GROUP BY를 사용했는데 오류가 나는 이유는 조건을 인식하지 못하기 때문. 즉, COUNT 등의 집계함수는 HAVING절과 사용 가능
# WHERE절을 먼저 사용하고 GROUP BY를 사용했을 때, 집계함수가 적용되지 않는 이유는 집계함수가 그룹함수이기 때문. 즉, 어떤 그룹에 대한 AVG, SUM 등을 구해야 하는데, WHERE는 개별 절에 조건이 걸리기 때문.

select grade, COUNT(*), ROUND(AVG(height)) avg_height,
        ROUND(AVG(weight)) avg_weight
FROM student
WHERE COUNT(*) > 4
GROUP BY grade
ORDER BY avg_height DESC;
----------------------------------------------------
ORA-00934: group function is not allowed here
00934. 00000 -  "group function is not allowed here"
*Cause:    
*Action:
18행, 7열에서 오류 발생

# 위의 코드를 GROUP BY로 먼저 그룹을 만들고 WHERE 대신, HAVING을 사용하여 조건을 설정하면, 집계함수를 사용하여 그룹 내의 MIN, MAX, SUM, AVG, COUNT 등을 확인할 수 있음.
select grade, COUNT(*), ROUND(AVG(height)) avg_height,
        ROUND(AVG(weight)) avg_weight
FROM student
GROUP BY grade
HAVING COUNT(*) > 4
ORDER BY avg_height DESC;
```



# EQUI JOIN

```sql
# 여러 테이블에 저장된 데이터를 한번에 조회
# 관계형 데이터베이스 분야의 표준
# 두개 이상의 테이블 결합
# 점(.)을 통해 구분
# 구문분석 시간(parsing time)을 줄이기 위해 점(.) 뒤에 바로 붙여주어야함.
# WHERE p.deptno = d.deptno; 처럼 연결 조건이 같아야 함. 즉, 같은 연결고리가 있어야함.

# EQUI JOIN
SELECT studno, name,
    student.deptno, department.deptno
FROM student, department
WHERE student.deptno = department.deptno;

SELECT studno, userid, name, student.deptno, department.dname
FROM student, department, department.loc
WHERE student.deptno = department.deptno;

# join 사용 시 별명 사용 가능. ex) student s
# 별명 사용 시, 모두 별명으로 표현 필요.

SELECT s.studno, s.name,
    s.deptno, d.deptno
FROM student s, department d
WHERE s.deptno = d.deptno;

# 조건을 추가할 수 있음
SELECT s.studno, s.name,
    s.deptno, d.deptno
FROM student s, department d
WHERE s.deptno = d.deptno
AND s.name = '전인하';

SELECT s.studno, s.name,
    s.deptno, d.deptno
FROM student s, department d
WHERE s.deptno = d.deptno
AND s.weight >= 80;

# 사원테이블, DALLAS 사번, 이름, 부서번호, 부서이름, 근무지
SELECT e.empno, e.ename, e.deptno, d.deptno, d.loc
FROM emp e, dept d
WHERE e.deptno = d.deptno
AND d.loc = 'DALLAS';

# 학생 이름, 학번, 학과번호, 지도교수 번호, 지도교수 이름
SELECT s.name "학생이름", s.idnum "학번", s.deptno "학과번호", p.profno "지도교수 번호", p.name "지도교수 이름"
FROM student s, professor p
WHERE s.deptno = p.deptno;

# 학생 이름, 학번, 학과번호, 지도교수 번호, 교수 이름, 학과이름, 학과 위치
SELECT s.name "학생이름", s.deptno "학과번호", 
    p.profno "지도교수 번호", p.name "지도교수 이름", d.dname "학과이름", d.loc "학과위치" 
FROM student s, professor p, department d
WHERE s.profno = p.profno
AND s.deptno = d.deptno;

# 학생 이름, 학번, 학과번호, 학생 몸무게, 지도교수 번호, 교수 이름, 학과이름, 학과 위치
SELECT s.name "학생이름", s.deptno "학과번호", 
    p.profno "지도교수 번호", p.name "지도교수 이름", d.dname "학과이름", d.loc "학과위치" 
FROM student s, professor p, department d
WHERE s.profno = p.profno
AND s.deptno = d.deptno
AND s.weight = 70;

# EQUI JOIN은 정확하게 일치하는 것만 가져옴
# 1. EQUI JOIN
SELECT s.studno, s.name, s.deptno, d.dname, d.loc
FROM student s, department d
WHERE s.deptno = d.deptno;

```



# NATURAL JOIN

1. NATUAL JOIN은 자동적으로 모든 컬럼을 대상으로 공통 컬럼을 선택 후 JOIN 생성 

```sql
# NATURAL JOIN
# oracle join은 NATURAL JOIN 이라고도 함. 
SELECT p.profno, p.name, deptno
FROM professor p
    NATURAL JOIN department d;

SELECT s.name, s.grade, deptno, d.dname
FROM student s
    NATURAL JOIN department d
WHERE grade = '4';

# deptno가 공통 컬럼으로 s.를 붙이면 오류 발생
SELECT s.studno, s.name, s.deptno, d.dname
FROM student s
    NATURAL JOIN department d;

# deptno에 애트리뷰트를 사용하지 않아야 정상적으로 작동
SELECT s.studno, s.name, deptno, d.dname
FROM student s
    NATURAL JOIN department d; 

# 전체 코드에 애트리뷰트를 사용하지 않아도 정상 작동
SELECT studno, name, deptno, dname
FROM student 
    NATURAL JOIN department;  
```



# EQUI JOIN USING

```sql
SELECT studno, name, deptno, dname, loc
FROM student JOIN department
USING (deptno);
```



## JOIN USING 비교

* 세 가지 형태의 성능 차이는 없고, 사용하는 방식의 차이만 있음
* WHERE 절을 주로 사용

```sql
# WHERE
SELECT name, dname, loc
FROM student s, department d
WHERE s.deptno = d.deptno
AND name like '김%';

# NATURAL JOIN
SELECT s.name, d.dname, d.loc
FROM student s 
        NATURAL JOIN department d
WHERE name like '김%';

# JOIN ~ USING
SELECT name, dname, loc
FROM student JOIN department
    USING(deptno)
WHERE name like '김%';

```



# INNER JOIN

```sql
# INNER JOIN
# 조건절에 ON 필요
SELECT name, dname, loc
FROM student s INNER JOIN department d
ON s.deptno = d.deptno;
```



# 카티션 곱

* 카티션 곱과 CROSS JOIN의 사용 형태만 다를 뿐, 성능 상에 차이는 없음

```sql
# 카티션 곱
# 두개 이상 연결 가능한 행 모두 결합
# 연결고리가 없어서 무작위로 연결될 수 있음
SELECT studno, name, s.deptno, d.deptno, dname
FROM student s, department d;

# cross join
SELECT studno, name, s.deptno, d.deptno, dname
FROM student s cross join department d;
```



# NON EQUI JOIN

```sql
# NON EQUI JOIN
# '<', BETWEEN a AND b와 같이 '=' 아닌 연산자 사용

SELECT p.profno, p.name, p.sal, s.grade
FROM professor p, salgrade s
WHERE p.sal BETWEEN s.losal AND s.hisal;
```



# OUTER JOIN

* JOIN하려는 값 중 어느 하나라고 NULL 값이 있으면, 비교결과가 거짓이 되어 NULL 값을 가진 행은 출력되지 않음.

```sql
# OUTER JOIN
# NULL 값이 발생할 때 주로 사용
# NULL이 출력되는 컬럼 옆에 (+) 입력

# 지도교수가 없는 학생
SELECT s.name, s.grade, p.name, p.position
FROM student s, professor p
WHERE s.profno = p.profno(+)
ORDER BY p.profno;

# 지도학생이 없는 교수, 지도교수가 없는 학생 합치기
# ORDER BY가 있으면 출력되지 않음
SELECT s.name, s.grade, p.name, p.position
FROM student s, professor p
WHERE s.profno = p.profno(+)
UNION
SELECT s.name, s.grade, p.name, p.position
FROM student s, professor p
WHERE s.profno(+) = p.profno;

# ORDER BY 1을 입력하면 출력 가능
SELECT s.name, s.grade, p.name, p.position
FROM student s, professor p
WHERE s.profno = p.profno(+)
UNION
SELECT s.name, s.grade, p.name, p.position
FROM student s, professor p
WHERE s.profno(+) = p.profno
ORDER BY 1;
```

## OUTER JOIN 비교

```sql
# 아래 코드만 실행할 경우, NULL값이 나오지 않음
SELECT s.name, s.grade, p.name, p.position
FROM student s, professor p
WHERE s.profno = p.profno
ORDER BY p.profno;

# (+)를 사용하면 NULL 값도 출력 가능
SELECT s.name, s.grade, p.name, p.position
FROM student s, professor p
WHERE s.profno = p.profno(+)
ORDER BY p.profno;
```







# LEFT OUTER JOIN

```sql
# LEFT OUTER JOIN
# 테이블 왼쪽으로 결합
SELECT studno, s.name, s.name, s.profno, p.name
FROM student s
    LEFT OUTER JOIN professor p
    ON s.profno = p.profno;
```



# SELF JOIN

* 조직도 데이터 관리에 주로 활용

```sql
# WHERE를 활용한 SELF JOIN
SELECT dept.dname || '의 소속은' || org.dname
FROM department dept, department org
where dept.college = org.deptno;

# JOIN ~ ON 활용한 SELF JOIN 
SELECT dept.dname || '의 소속은' || org.dname
FROM department dept JOIN department org
    ON dept.college = org.deptno;
```



# 문제풀이

```sql
1. 총 급여가 $5,000이 넘는 각 JOB에 대해 JOB과 월급 총액을
출력하세요. (단, PRESIDENT를 제외시키고, 월급 총액별으로 정렬)
JOB PAYROLL
------------------ ----------
SALESMAN 5600
ANALYST 6000
MANAGER 8275

SELECT job, SUM(sal)
FROM emp
WHERE job not in('PRESIDENT')
GROUP BY job
HAVING SUM(sal) > 5000
ORDER BY SUM(sal);

/* 2,3번 employees, departments, locations */
2. Shipping부서에 근무하는 사원에 대해 last_name, job_id,
부서번호, 부서이름을 last_name 순으로 출력하세요. (결과-45건)

SELECT e.LAST_NAME, e.JOB_ID, d.DEPARTMENT_ID, d.DEPARTMENT_NAME
FROM employees e, departments d
WHERE DEPARTMENT_NAME in 'Shipping'
ORDER BY LAST_NAME;

3. south san francisco에서 근무하는 모든 사원에 대해
last_name, job, 부서번호, 부서이름, 부서위치(city)를
출력하세요. (결과-45건)

LAST_NAME JOB_ID DEPARTMENT_ID DEPARTMENT_N CITY
---------- ---------- ------------- ------------ -------------------
Weiss ST_MAN 50 Shipping South San Francisco
Fripp ST_MAN 50 Shipping South San Francisco
Kaufling ST_MAN 50 Shipping South San Francisco
Vollman ST_MAN 50 Shipping South San Francisco
Mourgos ST_MAN 50 Shipping South San Francisco
Nayer ST_CLERK 50 Shipping South San Francisco
Mikkilinen ST_CLERK 50 Shipping South San Francisco

SELECT e.last_name, e.department_id, d.department_name, l.city
FROM employees e, departments d, locations l
WHERE e.department_id = d.department_id
AND d.location_id = l.location_id
AND CITY in 'South San Francisco';


4. 사원의 이름과 사원 번호 그리고 관리자 이름과
관리자 번호를 출력하세요.(열 레이블 Employee,
Emp#, Manager 그리고 Mgr#)
Employee Emp# Manager Mgr#
---------- ---------- ---------- ----------
FORD 7902 JONES 7566
SCOTT 7788 JONES 7566
               
               SELECT e.ename "Employee", e.empno "Emp#", e.ename "Manager", e.empno "Mgr#"
FROM emp e, emp em
WHERE e.mgr = em.empno;

```



# 연습 데이터 불러오기

```sql
SELECT * FROM emp;
SELECT * FROM student;
SELECT * FROM professor;
SELECT * FROM employees;
SELECT * FROM department;
SELECT * FROM departments;
SELECT * FROM locations;
SELECT * FROM salgrade;
```



# practice

```sql

# LEFT OUTER JOIN 활용 지도제자, 지도교수 매치
SELECT s.name "학생이름", s.grade "학년", 
        p.name "지도교수", p.position "직급"
FROM student s 
    LEFT OUTER JOIN professor p
    ON s.profno = p.profno;

# WHERE 활용 지도제자, 지도교수 매치
SELECT s.name, s.grade, p.name, p.position
FROM student s, professor p
WHERE s.profno = p.profno(+)
ORDER BY p.profno;
```





