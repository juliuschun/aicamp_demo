# Database 연결 가이드 (학생용)

> Claude Code로 데이터베이스에 자연어 질문하기

---

## 1. MCP 설정 (한 줄)

```bash
claude mcp add postgres-demo -- npx -y @modelcontextprotocol/server-postgres "postgresql://camp_student:npg_7dvjQGXHM2lW@ep-ancient-brook-ah8iwmvc-pooler.c-3.us-east-1.aws.neon.tech/neondb?sslmode=require"
```

> 읽기 전용 계정입니다. 데이터 수정 불가.

---

## 2. 바로 질문하기

설정 후 바로 사용 가능:
```
> 레고 테마별 세트 수 알려줘
> 부서별 평균 연봉 비교해줘
> 가장 부품 많은 레고 세트 TOP 10
```

---

## 3. 사용 가능한 데이터

### Lego Database
| 테이블 | 설명 | 행 수 |
|--------|------|-------|
| lego_sets | 레고 세트 정보 | 11,673 |
| lego_themes | 테마 (Star Wars, City 등) | 614 |
| lego_parts | 개별 부품 | 25,993 |
| lego_colors | 레고 색상 | 135 |

### Employees Database
| 테이블 | 설명 | 행 수 |
|--------|------|-------|
| employees.employee | 직원 기본 정보 | 300,024 |
| employees.department | 부서 정보 | 9 |
| employees.salary | 급여 이력 | 2,844,047 |
| employees.title | 직함 이력 | 443,308 |

---

## 4. 질문 예시

### Lego
- 테마별 세트 수 Top 10 알려줘
- 연도별 평균 부품 수 변화 보여줘
- 가장 부품 많은 세트 Top 5

### Employees
- 부서별 현재 직원 수 알려줘
- 부서별 평균 연봉 비교해줘
- 최근 10년 채용 트렌드 보여줘

> 더 많은 질문 예시: `lego_sql_manual.md`, `employees_sql_manual.md`

---

## 5. 문제 해결

### MCP 연결 안 됨
```bash
# 설정 확인
claude mcp list
```

### "relation does not exist" 에러
- Lego 테이블: `lego_sets` (lego_ 접두사 필요)
- Employees 테이블: `employees.employee` (스키마 명시 필요)
