# Neon Database Setup for AI Camp Demo

> 레고 + 직원 데이터베이스를 Neon에 세팅하고, 학생들에게 읽기 전용 접근 권한 부여

---

## 학생용 Quick Start

### MCP 설정 (한 줄)

```bash
claude mcp add postgres-demo -- npx -y @modelcontextprotocol/server-postgres "postgresql://camp_student:npg_7dvjQGXHM2lW@ep-ancient-brook-ah8iwmvc-pooler.c-3.us-east-1.aws.neon.tech/neondb?sslmode=require"
```

> 읽기 전용 계정입니다. 데이터 수정 불가.

설정 후 Claude Code 재시작, 그 다음:
```
> 레고 테마별 세트 수 알려줘
> 부서별 평균 연봉 비교해줘
```

### 데이터 현황

| Dataset | Table | Rows |
|---------|-------|------|
| Employees | salary | 2,844,047 |
| Employees | employee | 300,024 |
| Lego | lego_sets | 11,673 |
| Lego | lego_themes | 614 |

---

## 1. 연결 정보 (관리자용 - 비공개)

```bash
# 관리자 접속
psql 'postgresql://neondb_owner:<PASSWORD>@ep-ancient-brook-ah8iwmvc-pooler.c-3.us-east-1.aws.neon.tech/neondb?sslmode=require'
```

> ⚠️ 실제 비밀번호는 이 파일에 저장하지 말 것. 환경변수나 별도 보안 저장소 사용.

---

## 2. 데이터베이스 임포트 (완료)

### 2-1. Lego Database (633K rows, 42MB)

```bash
# 다운로드
wget https://raw.githubusercontent.com/neondatabase/postgres-sample-dbs/main/lego.sql

# 임포트 (neondb에 직접 또는 별도 DB 생성 후)
psql 'postgresql://neondb_owner:<PASSWORD>@ep-ancient-brook-ah8iwmvc-pooler.c-3.us-east-1.aws.neon.tech/neondb?sslmode=require' -f lego.sql
```

**테이블 구조:**
| 테이블 | 설명 | 예상 rows |
|--------|------|-----------|
| themes | 레고 테마 (Star Wars, City 등) | 600+ |
| sets | 레고 세트 정보 | 20,000+ |
| parts | 개별 부품 | 50,000+ |
| colors | 레고 색상 | 200+ |
| inventories | 세트별 인벤토리 | 20,000+ |
| inventory_parts | 인벤토리-부품 매핑 | 580,000+ |
| inventory_sets | 인벤토리-세트 매핑 | 4,000+ |
| part_categories | 부품 카테고리 | 60+ |

### 2-2. Employees Database (3.9M rows, 333MB)

```bash
# 다운로드
wget https://raw.githubusercontent.com/neondatabase/postgres-sample-dbs/main/employees.sql.gz

# 임포트 (pg_restore 사용)
pg_restore -d 'postgresql://neondb_owner:<PASSWORD>@ep-ancient-brook-ah8iwmvc-pooler.c-3.us-east-1.aws.neon.tech/neondb' \
  -Fc employees.sql.gz --no-owner --no-privileges
```

> 주의: Employees 데이터는 `employees` 스키마에 생성됨 (`public`이 아님)

**테이블 구조:**
| 테이블 | 설명 | 예상 rows |
|--------|------|-----------|
| employees.employees | 직원 기본 정보 | 300,000 |
| employees.departments | 부서 정보 | 9 |
| employees.dept_emp | 직원-부서 매핑 | 331,000+ |
| employees.dept_manager | 부서 매니저 이력 | 24 |
| employees.salaries | 급여 이력 | 2,800,000+ |
| employees.titles | 직함 이력 | 443,000+ |

---

## 3. 읽기 전용 사용자 생성 (학생용)

### 3-1. psql 접속

```bash
psql 'postgresql://neondb_owner:<PASSWORD>@ep-ancient-brook-ah8iwmvc-pooler.c-3.us-east-1.aws.neon.tech/neondb?sslmode=require'
```

### 3-2. 읽기 전용 사용자 생성

```sql
-- 1. 읽기 전용 사용자 생성
CREATE USER camp_student WITH PASSWORD 'aicamp2026!';

-- 2. 데이터베이스 연결 권한
GRANT CONNECT ON DATABASE neondb TO camp_student;

-- 3. public 스키마 (Lego 테이블) 읽기 권한
GRANT USAGE ON SCHEMA public TO camp_student;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO camp_student;

-- 4. employees 스키마 읽기 권한
GRANT USAGE ON SCHEMA employees TO camp_student;
GRANT SELECT ON ALL TABLES IN SCHEMA employees TO camp_student;

-- 5. 향후 생성되는 테이블에도 자동 권한 부여
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES TO camp_student;
ALTER DEFAULT PRIVILEGES IN SCHEMA employees GRANT SELECT ON TABLES TO camp_student;
```

### 3-3. 권한 확인

```sql
-- 현재 사용자 권한 확인
\du camp_student

-- 테이블 권한 확인
SELECT grantee, table_schema, table_name, privilege_type
FROM information_schema.table_privileges
WHERE grantee = 'camp_student';
```

---

## 4. 학생용 연결 문자열

```bash
# 학생 배포용 (읽기 전용)
postgresql://camp_student:npg_7dvjQGXHM2lW@ep-ancient-brook-ah8iwmvc-pooler.c-3.us-east-1.aws.neon.tech/neondb?sslmode=require
```

### Claude Code에서 사용 (MCP 방식 - 권장)

```bash
claude mcp add postgres-demo -- npx -y @modelcontextprotocol/server-postgres "postgresql://camp_student:npg_7dvjQGXHM2lW@ep-ancient-brook-ah8iwmvc-pooler.c-3.us-east-1.aws.neon.tech/neondb?sslmode=require"
```

> 읽기 전용 계정입니다. SELECT만 가능.

설정 후 Claude Code 재시작 필요. 이후 자연어로 질문 가능.

---

## 5. 샘플 쿼리 (데모용)

### Lego 데이터베이스

```sql
-- 테마별 세트 수 Top 10
SELECT t.name AS theme, COUNT(*) AS set_count
FROM sets s
JOIN themes t ON s.theme_id = t.id
GROUP BY t.name
ORDER BY set_count DESC
LIMIT 10;

-- 연도별 평균 부품 수 변화
SELECT year, ROUND(AVG(num_parts), 1) AS avg_parts
FROM sets
WHERE year >= 2000
GROUP BY year
ORDER BY year;

-- 가장 부품이 많은 세트 Top 5
SELECT name, year, num_parts
FROM sets
ORDER BY num_parts DESC
LIMIT 5;
```

### Employees 데이터베이스

```sql
-- 부서별 현재 직원 수
SELECT d.dept_name, COUNT(*) AS emp_count
FROM employees.departments d
JOIN employees.dept_emp de ON d.dept_no = de.dept_no
WHERE de.to_date = '9999-01-01'
GROUP BY d.dept_name
ORDER BY emp_count DESC;

-- 부서별 평균 연봉
SELECT d.dept_name, ROUND(AVG(s.salary), 0) AS avg_salary
FROM employees.departments d
JOIN employees.dept_emp de ON d.dept_no = de.dept_no
JOIN employees.salaries s ON de.emp_no = s.emp_no
WHERE de.to_date = '9999-01-01' AND s.to_date = '9999-01-01'
GROUP BY d.dept_name
ORDER BY avg_salary DESC;

-- 최근 10년 채용 트렌드
SELECT EXTRACT(YEAR FROM hire_date) AS year, COUNT(*) AS hires
FROM employees.employees
GROUP BY year
ORDER BY year DESC
LIMIT 10;
```

---

## 6. 트러블슈팅

### 연결 오류

```
connection refused
→ sslmode=require 확인

authentication failed
→ 비밀번호 확인, 특수문자 URL 인코딩 필요할 수 있음

permission denied for table
→ GRANT SELECT 명령 재실행
```

### 스키마 관련

```sql
-- employees 스키마 테이블 조회 시
SELECT * FROM employees.employees LIMIT 5;  -- O
SELECT * FROM employees LIMIT 5;            -- X (스키마 명시 필요)

-- 또는 search_path 설정
SET search_path TO employees, public;
```

---

## 7. 정리 (캠프 종료 후)

```sql
-- 학생 계정 삭제
REVOKE ALL PRIVILEGES ON ALL TABLES IN SCHEMA public FROM camp_student;
REVOKE ALL PRIVILEGES ON ALL TABLES IN SCHEMA employees FROM camp_student;
DROP USER camp_student;
```

---

*Last updated: 2026-01-14*
