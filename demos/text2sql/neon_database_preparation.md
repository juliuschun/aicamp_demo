# Database Preparation (관리자용)

> 캠프 전 DB 세팅 및 학생 계정 관리

---

## 1. Neon 연결 정보

```bash
# 관리자 접속
psql 'postgresql://neondb_owner:<PASSWORD>@ep-ancient-brook-ah8iwmvc-pooler.c-3.us-east-1.aws.neon.tech/neondb?sslmode=require'
```

> 실제 비밀번호는 환경변수나 별도 보안 저장소에서 관리

---

## 2. 데이터베이스 임포트

### Lego Database (633K rows)

```bash
wget https://raw.githubusercontent.com/neondatabase/postgres-sample-dbs/main/lego.sql

psql 'postgresql://neondb_owner:<PASSWORD>@ep-ancient-brook-ah8iwmvc-pooler.c-3.us-east-1.aws.neon.tech/neondb?sslmode=require' -f lego.sql
```

### Employees Database (3.9M rows)

```bash
wget https://raw.githubusercontent.com/neondatabase/postgres-sample-dbs/main/employees.sql.gz

pg_restore -d 'postgresql://neondb_owner:<PASSWORD>@...' \
  -Fc employees.sql.gz --no-owner --no-privileges
```

> `employees` 스키마에 생성됨 (public 아님)

---

## 3. 학생 계정 생성

```sql
CREATE USER camp_student WITH PASSWORD 'your_password_here';

GRANT CONNECT ON DATABASE neondb TO camp_student;

GRANT USAGE ON SCHEMA public TO camp_student;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO camp_student;

GRANT USAGE ON SCHEMA employees TO camp_student;
GRANT SELECT ON ALL TABLES IN SCHEMA employees TO camp_student;

ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES TO camp_student;
ALTER DEFAULT PRIVILEGES IN SCHEMA employees GRANT SELECT ON TABLES TO camp_student;
```

---

## 4. 캠프 종료 후 정리

```sql
REVOKE ALL PRIVILEGES ON ALL TABLES IN SCHEMA public FROM camp_student;
REVOKE ALL PRIVILEGES ON ALL TABLES IN SCHEMA employees FROM camp_student;
DROP USER camp_student;
```
