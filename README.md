# AI 자동화 실전 캠프

> Clone하면 바로 실습 가능

## 시작하기

```bash
# 1. 레포 클론
git clone https://github.com/YOUR_REPO/AI_camp.git
cd AI_camp

# 2. Claude Code 설치
npm install -g @anthropic-ai/claude-code

# 3. 커맨드 템플릿 설치
mkdir -p .claude/commands
cp templates/commands/*.md .claude/commands/
```

## 폴더 구조

```
AI_camp/
├── demos/
│   ├── text2sql/           # DB 연결 실습 (레고, 직원 DB)
│   └── manual_demo/        # 문서 자동 생성 실습
├── templates/
│   └── commands/           # 슬래시 커맨드 (prime, research, codify-this, to-word)
├── claude_code_korean_guide.md
├── claude_code_cheatsheet_kr.md
├── executive_ai_guide.md
└── executive_ai_philosophy.md
```

## DB 연결 (Text-to-SQL 실습)

```bash
# MCP 서버 추가
claude mcp add postgres-demo -- npx -y @modelcontextprotocol/server-postgres "postgresql://camp_student:npg_7dvjQGXHM2lW@ep-ancient-brook-ah8iwmvc-pooler.c-3.us-east-1.aws.neon.tech/neondb?sslmode=require"
```

Claude Code 재시작 후 자연어로 질문:
```
> 레고 테마별 세트 수 알려줘
> 부서별 평균 연봉 비교해줘
```

읽기 전용 계정이니 마음껏 실험하세요.

## 핵심 메시지

1. **코드 몰라도 된다** - AI가 써준다
2. **겁먹지 마라** - 모르면 AI에게 물어봐라
3. **키워드만 알면 된다** - AI가 구체화해준다
4. **배움을 보존하라** - Compounding Work
