# AI 자동화 실전 캠프 - 실습 자료

> 이 레포를 clone하면 바로 실습 가능합니다.

---

## 시작하기

### 1. 레포 클론

```bash
git clone https://github.com/YOUR_REPO/AI_camp.git
cd AI_camp
```

### 2. Claude Code 설치

```bash
# macOS
brew install claude-code

# 또는 npm
npm install -g @anthropic-ai/claude-code
```

### 3. 커맨드 템플릿 설치

`templates/commands/` 폴더의 파일들을 프로젝트의 `.claude/commands/`에 복사:

```bash
mkdir -p .claude/commands
cp templates/commands/*.md .claude/commands/
```

이제 `/research`, `/to-pdf` 등의 슬래시 커맨드를 사용할 수 있습니다.

---

## 폴더 구조

```
AI_camp/
├── demos/                          # 실습용 데모 자료
│   ├── neon_database_setup.md      # DB 연결 설정 가이드
│   ├── lego_sql_manual.md          # 레고 DB 쿼리 매뉴얼
│   ├── employees_sql_manual.md     # 직원 DB 쿼리 매뉴얼
│   └── manual_demo/                # 문서 자동 생성 예시
│
├── templates/                      # Claude Code 템플릿
│   ├── commands/                   # 슬래시 커맨드 모음
│   └── project/                    # 프로젝트 설정 파일 (CLAUDE.md 등)
│
├── claude_code_korean_guide.md     # Claude Code 한국어 완벽 가이드
├── claude_code_cheatsheet_kr.md    # Claude Code 치트시트 (빠른 참조)
├── executive_ai_guide.md           # 경영자를 위한 AI 가이드
└── executive_ai_philosophy.md      # AI 활용 철학
```

---

## templates/commands 사용법

### 설치 후 사용 가능한 커맨드

| 커맨드 | 설명 | 예시 |
|--------|------|------|
| `/research` | 주제에 대한 리서치 수행 | `/research AI 에이전트 트렌드 2025` |
| `/to-report` | 마크다운을 보고서 형식으로 변환 | `/to-report research_결과.md` |
| `/to-pdf` | 마크다운을 PDF로 변환 | `/to-pdf 보고서.md` |
| `/fetch-github` | GitHub 자료 가져오기 | `/fetch-github https://github.com/...` |
| `/prime` | 프로젝트 맥락 주입 | `/prime` |

### 커맨드 커스터마이징

`templates/commands/` 안의 `.md` 파일을 수정하여 나만의 워크플로우를 만들 수 있습니다.

---

## demos/ 실습 자료

### 데이터베이스 연결

PostgreSQL MCP 서버를 추가하면 자연어로 DB 쿼리가 가능합니다.

**1단계: MCP 서버 추가**

```bash
claude mcp add postgres-demo -- npx -y @modelcontextprotocol/server-postgres "postgresql://camp_student:npg_7dvjQGXHM2lW@ep-ancient-brook-ah8iwmvc-pooler.c-3.us-east-1.aws.neon.tech/neondb?sslmode=require"
```

**2단계: Claude Code 재시작**

터미널에서 `claude`를 다시 실행하거나 새 세션을 시작합니다.

**3단계: 자연어로 질문**

```
> 레고 테마별 세트 수 알려줘
> 부서별 평균 연봉 비교해줘
> 가장 부품이 많은 레고 세트 TOP 10
```

> 이 계정은 **읽기 전용**입니다. 데이터 수정이 불가능하니 마음껏 실험하세요.

### Lego Database (633K rows)

레고 세트, 테마, 부품, 색상 정보를 담은 샘플 데이터베이스입니다.

| 테이블 | 설명 |
|--------|------|
| lego_sets | 레고 세트 정보 (11,673개) |
| lego_themes | 테마 (Star Wars, City 등) |
| lego_parts | 개별 부품 |
| lego_colors | 레고 색상 |

매뉴얼: [demos/lego_sql_manual.md](demos/lego_sql_manual.md)

### Employees Database (3.9M rows)

가상의 회사 직원 정보를 담은 대용량 샘플 데이터베이스입니다.

| 테이블 | 설명 |
|--------|------|
| employees.employee | 직원 기본 정보 |
| employees.department | 부서 정보 |
| employees.salary | 급여 이력 |
| employees.title | 직책 이력 |

매뉴얼: [demos/employees_sql_manual.md](demos/employees_sql_manual.md)

---

## 학습 자료

- **claude_code_korean_guide.md**: Claude Code의 모든 기능을 상세히 설명
- **claude_code_cheatsheet_kr.md**: 자주 쓰는 명령어 빠른 참조
- **executive_ai_guide.md**: 경영자 관점의 AI 활용 전략
- **executive_ai_philosophy.md**: AI를 "부리는" 마인드셋

---

## 핵심 메시지

1. **코드 몰라도 된다** - AI가 써준다
2. **겁먹지 마라** - 모르면 AI에게 물어봐라
3. **키워드만 알면 된다** - AI가 구체화해준다
4. **배움을 보존하라** - Compounding Work

---

## 문의

캠프 관련 문의는 강사에게 직접 연락해주세요.
