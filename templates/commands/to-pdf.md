---
description: 마크다운 파일을 PDF로 변환
argument-hint: [파일경로]
---
$1 파일을 PDF로 변환해줘:

1. 파일이 존재하는지 확인
2. md-to-pdf로 PDF 생성 (uv로 자동 설치):
   ```bash
   uv run --with md-to-pdf md-to-pdf "$1"
   ```
3. 또는 Python 기반 방법:
   ```bash
   uv run --with markdown-pdf markdown_pdf "$1"
   ```
4. 성공하면: "✅ PDF 생성 완료: [파일명].pdf"

참고: uv run은 필요한 패키지를 자동으로 설치하고 실행합니다.
