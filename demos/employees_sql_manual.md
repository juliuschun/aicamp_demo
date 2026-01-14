# Employees Database SQL Manual

> AI 캠프 실습용 직원 데이터베이스 쿼리 매뉴얼

---

## 데이터베이스 개요

| 테이블 | 설명 | 행 수 |
|--------|------|-------|
| employees.employee | 직원 기본 정보 | 300,024 |
| employees.department | 부서 정보 | 9 |
| employees.department_employee | 직원-부서 매핑 | 331,000+ |
| employees.department_manager | 부서 매니저 이력 | 24 |
| employees.salary | 급여 이력 | 2,844,047 |
| employees.title | 직함 이력 | 443,000+ |

> **중요**: 현재 유효한 레코드는 `to_date = '9999-01-01'`로 표시됨

---

## 스키마 다이어그램

```
employees.department (id, dept_name)
     │
     ├──< employees.department_employee (employee_id, department_id, from_date, to_date)
     │              │
     │              └──> employees.employee (id, birth_date, first_name, last_name, gender, hire_date)
     │                            │
     │                            ├──< employees.salary (employee_id, amount, from_date, to_date)
     │                            │
     │                            └──< employees.title (employee_id, title, from_date, to_date)
     │
     └──< employees.department_manager (employee_id, department_id, from_date, to_date)
```

---

## 핵심 쿼리 10선 (테스트 완료)

### Q1: 부서별 현재 직원 수

각 부서에 현재 소속된 직원 수 집계

```sql
SELECT
    d.dept_name AS 부서명,
    COUNT(*) AS 직원수
FROM employees.department_employee de
JOIN employees.department d ON de.department_id = d.id
WHERE de.to_date = '9999-01-01'
GROUP BY d.dept_name
ORDER BY 직원수 DESC;
```

### Q2: 부서별 현재 평균 급여 및 급여 범위

부서별 급여 통계 분석

```sql
SELECT
    d.dept_name AS 부서명,
    ROUND(AVG(s.amount)) AS 평균급여,
    MIN(s.amount) AS 최저급여,
    MAX(s.amount) AS 최고급여,
    COUNT(*) AS 직원수
FROM employees.salary s
JOIN employees.department_employee de ON s.employee_id = de.employee_id
JOIN employees.department d ON de.department_id = d.id
WHERE s.to_date = '9999-01-01'
  AND de.to_date = '9999-01-01'
GROUP BY d.dept_name
ORDER BY 평균급여 DESC;
```

### Q3: 현재 최고 연봉자 TOP 20

가장 높은 급여를 받는 직원 목록

```sql
SELECT
    e.first_name || ' ' || e.last_name AS 직원명,
    d.dept_name AS 부서명,
    t.title AS 직책,
    s.amount AS 현재급여
FROM employees.employee e
JOIN employees.salary s ON e.id = s.employee_id
JOIN employees.department_employee de ON e.id = de.employee_id
JOIN employees.department d ON de.department_id = d.id
JOIN employees.title t ON e.id = t.employee_id
WHERE s.to_date = '9999-01-01'
  AND de.to_date = '9999-01-01'
  AND t.to_date = '9999-01-01'
ORDER BY s.amount DESC
LIMIT 20;
```

### Q4: 부서별 성별 분포 및 평균 급여 차이

성별 다양성 및 급여 형평성 분석

```sql
SELECT
    d.dept_name AS 부서명,
    e.gender AS 성별,
    COUNT(*) AS 직원수,
    ROUND(AVG(s.amount)) AS 평균급여,
    ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER (PARTITION BY d.dept_name), 1) AS 비율
FROM employees.employee e
JOIN employees.salary s ON e.id = s.employee_id
JOIN employees.department_employee de ON e.id = de.employee_id
JOIN employees.department d ON de.department_id = d.id
WHERE s.to_date = '9999-01-01'
  AND de.to_date = '9999-01-01'
GROUP BY d.dept_name, e.gender
ORDER BY d.dept_name, e.gender;
```

### Q5: 입사년도별 현재 직원 수 및 평균 급여

채용 코호트별 급여 성장 분석

```sql
SELECT
    EXTRACT(YEAR FROM e.hire_date) AS 입사년도,
    COUNT(*) AS 직원수,
    ROUND(AVG(s.amount)) AS 평균급여,
    MIN(s.amount) AS 최저급여,
    MAX(s.amount) AS 최고급여
FROM employees.employee e
JOIN employees.salary s ON e.id = s.employee_id
WHERE s.to_date = '9999-01-01'
GROUP BY EXTRACT(YEAR FROM e.hire_date)
ORDER BY 입사년도
LIMIT 20;
```

### Q6: 직책별 현재 직원 수 및 평균 급여

직책별 보상 구조 분석

```sql
SELECT
    t.title AS 직책,
    COUNT(*) AS 직원수,
    ROUND(AVG(s.amount)) AS 평균급여,
    MIN(s.amount) AS 최저급여,
    MAX(s.amount) AS 최고급여,
    ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER (), 1) AS 비율
FROM employees.title t
JOIN employees.salary s ON t.employee_id = s.employee_id
WHERE t.to_date = '9999-01-01'
  AND s.to_date = '9999-01-01'
GROUP BY t.title
ORDER BY 평균급여 DESC;
```

### Q7: 부서별 역대 매니저 정보 및 재임 기간

경영진 변동 이력 추적

```sql
SELECT
    d.dept_name AS 부서명,
    e.first_name || ' ' || e.last_name AS 매니저명,
    dm.from_date AS 취임일,
    dm.to_date AS 퇴임일,
    CASE
        WHEN dm.to_date = '9999-01-01' THEN '현재 재임'
        ELSE CAST(dm.to_date - dm.from_date AS TEXT) || '일'
    END AS 재임기간,
    s.amount AS 현재급여
FROM employees.department_manager dm
JOIN employees.department d ON dm.department_id = d.id
JOIN employees.employee e ON dm.employee_id = e.id
LEFT JOIN employees.salary s ON e.id = s.employee_id AND s.to_date = '9999-01-01'
ORDER BY d.dept_name, dm.from_date DESC
LIMIT 20;
```

### Q8: 급여 인상률이 가장 높은 직원 TOP 20

최고 성장률 직원 분석

```sql
WITH salary_growth AS (
    SELECT
        employee_id,
        MIN(CASE WHEN from_date = (SELECT MIN(from_date) FROM employees.salary s2 WHERE s2.employee_id = s.employee_id) THEN amount END) AS 초기급여,
        MAX(CASE WHEN to_date = '9999-01-01' THEN amount END) AS 현재급여
    FROM employees.salary s
    GROUP BY employee_id
    HAVING MIN(CASE WHEN from_date = (SELECT MIN(from_date) FROM employees.salary s2 WHERE s2.employee_id = s.employee_id) THEN amount END) IS NOT NULL
       AND MAX(CASE WHEN to_date = '9999-01-01' THEN amount END) IS NOT NULL
)
SELECT
    e.first_name || ' ' || e.last_name AS 직원명,
    sg.초기급여,
    sg.현재급여,
    sg.현재급여 - sg.초기급여 AS 급여증가액,
    ROUND(100.0 * (sg.현재급여 - sg.초기급여) / sg.초기급여, 1) AS 인상률
FROM salary_growth sg
JOIN employees.employee e ON sg.employee_id = e.id
WHERE sg.초기급여 > 0
ORDER BY 인상률 DESC
LIMIT 20;
```

### Q9: 연도별 채용 트렌드 및 성별 분포

HR 채용 전략 분석용

```sql
SELECT
    EXTRACT(YEAR FROM hire_date) AS 채용년도,
    COUNT(*) AS 총채용수,
    SUM(CASE WHEN gender = 'M' THEN 1 ELSE 0 END) AS 남성,
    SUM(CASE WHEN gender = 'F' THEN 1 ELSE 0 END) AS 여성,
    ROUND(100.0 * SUM(CASE WHEN gender = 'F' THEN 1 ELSE 0 END) / COUNT(*), 1) AS 여성비율
FROM employees.employee
GROUP BY EXTRACT(YEAR FROM hire_date)
ORDER BY 채용년도
LIMIT 20;
```

### Q10: 부서별 직책 분포

조직 구조 및 인력 배치 분석

```sql
SELECT
    d.dept_name AS 부서명,
    t.title AS 직책,
    COUNT(*) AS 직원수,
    ROUND(AVG(s.amount)) AS 평균급여
FROM employees.department_employee de
JOIN employees.department d ON de.department_id = d.id
JOIN employees.title t ON de.employee_id = t.employee_id
JOIN employees.salary s ON de.employee_id = s.employee_id
WHERE de.to_date = '9999-01-01'
  AND t.to_date = '9999-01-01'
  AND s.to_date = '9999-01-01'
GROUP BY d.dept_name, t.title
HAVING COUNT(*) > 1000
ORDER BY d.dept_name, 평균급여 DESC
LIMIT 20;
```

---

## 추가 샘플 쿼리 20선

### 기본 조회

```sql
-- 11: 전체 직원 수 확인
SELECT COUNT(*) AS 총직원수 FROM employees.employee;

-- 12: 전체 부서 목록
SELECT id, dept_name FROM employees.department ORDER BY dept_name;

-- 13: 특정 부서 직원 조회 (Sales)
SELECT e.first_name, e.last_name, e.hire_date
FROM employees.employee e
JOIN employees.department_employee de ON e.id = de.employee_id
JOIN employees.department d ON de.department_id = d.id
WHERE d.dept_name = 'Sales' AND de.to_date = '9999-01-01'
LIMIT 20;

-- 14: 이름에 'John' 포함된 직원
SELECT first_name, last_name, hire_date
FROM employees.employee
WHERE first_name LIKE 'John%'
LIMIT 20;

-- 15: 1990년 이후 입사자
SELECT first_name, last_name, hire_date
FROM employees.employee
WHERE hire_date >= '1990-01-01'
ORDER BY hire_date
LIMIT 20;
```

### 집계 및 통계

```sql
-- 16: 성별 직원 수
SELECT gender AS 성별, COUNT(*) AS 직원수 FROM employees.employee GROUP BY gender;

-- 17: 연도별 퇴사자 수 (부서 이동 포함)
SELECT EXTRACT(YEAR FROM to_date) AS 연도, COUNT(*) AS 이동수
FROM employees.department_employee
WHERE to_date != '9999-01-01'
GROUP BY EXTRACT(YEAR FROM to_date)
ORDER BY 연도 DESC LIMIT 20;

-- 18: 평균 급여 이상 받는 직원 수
SELECT COUNT(*) AS 평균이상직원수
FROM employees.salary
WHERE to_date = '9999-01-01'
  AND amount > (SELECT AVG(amount) FROM employees.salary WHERE to_date = '9999-01-01');

-- 19: 직책별 평균 재직 기간
SELECT title AS 직책,
    ROUND(AVG(to_date - from_date)) AS 평균재직일수
FROM employees.title
WHERE to_date != '9999-01-01'
GROUP BY title
ORDER BY 평균재직일수 DESC;

-- 20: 급여 분포 (구간별)
SELECT
    CASE
        WHEN amount < 40000 THEN '~40K'
        WHEN amount < 60000 THEN '40K-60K'
        WHEN amount < 80000 THEN '60K-80K'
        WHEN amount < 100000 THEN '80K-100K'
        ELSE '100K+'
    END AS 급여구간,
    COUNT(*) AS 직원수
FROM employees.salary
WHERE to_date = '9999-01-01'
GROUP BY 급여구간
ORDER BY 급여구간;
```

### JOIN 활용

```sql
-- 21: 직원 상세 정보 (부서, 직책, 급여 포함)
SELECT e.first_name || ' ' || e.last_name AS 이름,
    d.dept_name AS 부서, t.title AS 직책, s.amount AS 급여
FROM employees.employee e
JOIN employees.department_employee de ON e.id = de.employee_id
JOIN employees.department d ON de.department_id = d.id
JOIN employees.title t ON e.id = t.employee_id
JOIN employees.salary s ON e.id = s.employee_id
WHERE de.to_date = '9999-01-01' AND t.to_date = '9999-01-01' AND s.to_date = '9999-01-01'
LIMIT 20;

-- 22: 현재 매니저와 그들의 부서
SELECT d.dept_name AS 부서, e.first_name || ' ' || e.last_name AS 매니저
FROM employees.department_manager dm
JOIN employees.department d ON dm.department_id = d.id
JOIN employees.employee e ON dm.employee_id = e.id
WHERE dm.to_date = '9999-01-01';

-- 23: 같은 날 입사한 직원들
SELECT hire_date AS 입사일, COUNT(*) AS 동기수
FROM employees.employee
GROUP BY hire_date
HAVING COUNT(*) > 50
ORDER BY 동기수 DESC LIMIT 20;

-- 24: 부서 이동 이력이 있는 직원
SELECT e.first_name || ' ' || e.last_name AS 이름, COUNT(*) AS 부서이동횟수
FROM employees.employee e
JOIN employees.department_employee de ON e.id = de.employee_id
GROUP BY e.id, e.first_name, e.last_name
HAVING COUNT(*) > 1
ORDER BY 부서이동횟수 DESC LIMIT 20;

-- 25: 가장 오래 근무 중인 직원
SELECT e.first_name || ' ' || e.last_name AS 이름, e.hire_date AS 입사일,
    CURRENT_DATE - e.hire_date AS 근무일수
FROM employees.employee e
ORDER BY e.hire_date LIMIT 20;
```

### 고급 분석

```sql
-- 26: 부서별 급여 순위
SELECT d.dept_name AS 부서, e.first_name || ' ' || e.last_name AS 이름, s.amount AS 급여,
    RANK() OVER (PARTITION BY d.dept_name ORDER BY s.amount DESC) AS 부서내순위
FROM employees.employee e
JOIN employees.department_employee de ON e.id = de.employee_id
JOIN employees.department d ON de.department_id = d.id
JOIN employees.salary s ON e.id = s.employee_id
WHERE de.to_date = '9999-01-01' AND s.to_date = '9999-01-01'
LIMIT 20;

-- 27: 연도별 평균 급여 변화
SELECT EXTRACT(YEAR FROM from_date) AS 연도, ROUND(AVG(amount)) AS 평균급여
FROM employees.salary
GROUP BY EXTRACT(YEAR FROM from_date)
ORDER BY 연도 LIMIT 20;

-- 28: 직책 승진 패턴 (Engineer → Senior Engineer 등)
SELECT t1.title AS 이전직책, t2.title AS 현재직책, COUNT(*) AS 건수
FROM employees.title t1
JOIN employees.title t2 ON t1.employee_id = t2.employee_id AND t1.to_date = t2.from_date
GROUP BY t1.title, t2.title
ORDER BY 건수 DESC LIMIT 20;

-- 29: 부서별 급여 편차 (표준편차)
SELECT d.dept_name AS 부서,
    ROUND(AVG(s.amount)) AS 평균급여,
    ROUND(STDDEV(s.amount)) AS 급여편차
FROM employees.salary s
JOIN employees.department_employee de ON s.employee_id = de.employee_id
JOIN employees.department d ON de.department_id = d.id
WHERE s.to_date = '9999-01-01' AND de.to_date = '9999-01-01'
GROUP BY d.dept_name
ORDER BY 급여편차 DESC;

-- 30: 나이대별 직원 분포
SELECT
    CASE
        WHEN EXTRACT(YEAR FROM age(birth_date)) < 40 THEN '30대'
        WHEN EXTRACT(YEAR FROM age(birth_date)) < 50 THEN '40대'
        WHEN EXTRACT(YEAR FROM age(birth_date)) < 60 THEN '50대'
        ELSE '60대+'
    END AS 나이대,
    COUNT(*) AS 직원수,
    ROUND(AVG(s.amount)) AS 평균급여
FROM employees.employee e
JOIN employees.salary s ON e.id = s.employee_id
WHERE s.to_date = '9999-01-01'
GROUP BY 나이대
ORDER BY 나이대;
```

---

## SQL 기능별 인덱스

| 기능 | 쿼리 번호 |
|------|-----------|
| SELECT 기본 | 11, 12, 13, 14, 15 |
| COUNT, SUM, AVG | 1, 2, 5, 6, 9, 16, 17 |
| JOIN | 2, 3, 4, 10, 21, 22, 24, 25 |
| GROUP BY | 1, 2, 4, 5, 6, 9, 10, 16, 17, 19, 20, 23 |
| ORDER BY | 모든 쿼리 |
| LIMIT | 대부분 쿼리 |
| WHERE 조건 | 대부분 쿼리 (to_date = '9999-01-01') |
| CASE WHEN | 4, 7, 9, 20, 30 |
| 서브쿼리 | 8, 18 |
| 윈도우 함수 | 4, 6, 26 |
| CTE (WITH) | 8 |
| HAVING | 10, 23, 24 |
| 날짜 함수 | 5, 9, 17, 25, 27, 30 |
| STDDEV | 29 |

---

## 주요 패턴

### 현재 데이터만 조회하기

```sql
WHERE to_date = '9999-01-01'
```

### 다중 테이블 현재 데이터 조회

```sql
WHERE s.to_date = '9999-01-01'
  AND de.to_date = '9999-01-01'
  AND t.to_date = '9999-01-01'
```

### 이름 합치기

```sql
e.first_name || ' ' || e.last_name AS 이름
```

---

*Last updated: 2026-01-14*
