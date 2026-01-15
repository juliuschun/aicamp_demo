# Employees Database

> Claude Code에게 자연어로 질문하면 SQL을 자동 생성합니다.

---

## 스키마 가이드 (AI 참고용)

### 테이블 & 컬럼 (검증 완료)

| 테이블 | 컬럼 | rows |
|--------|------|------|
| employees.employee | id, birth_date, first_name, last_name, gender, hire_date | 300,024 |
| employees.department | id, dept_name | 9 |
| employees.department_employee | employee_id, department_id, from_date, to_date | 331,603 |
| employees.department_manager | employee_id, department_id, from_date, to_date | 24 |
| employees.salary | employee_id, amount, from_date, to_date | 2,844,047 |
| employees.title | employee_id, title, from_date, to_date | 443,308 |

### 핵심 규칙
- **스키마 prefix 필수**: `employees.employee` (public 아님)
- **현재 데이터 필터**: `WHERE to_date = '9999-01-01'`
- **급여 컬럼**: `amount` (salary 아님)

### JOIN 패턴 (테스트 완료)

```sql
-- 직원 → 부서
employees.employee.id = employees.department_employee.employee_id
employees.department_employee.department_id = employees.department.id

-- 직원 → 급여
employees.employee.id = employees.salary.employee_id

-- 직원 → 직책
employees.employee.id = employees.title.employee_id
```

---

## 질문 예시

- 전체 직원 수 알려줘
- 부서별 현재 직원 수
- 부서별 평균 급여 비교
- 현재 최고 연봉자 TOP 20
- 직책별 직원 수와 평균 급여
- 현재 각 부서 매니저는 누구?
