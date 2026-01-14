---
description: 마크다운을 워드 문서로 변환
argument-hint: [파일경로]
---
$1 파일을 워드(.docx)로 변환해줘:

1. 파일 존재 확인
2. python-docx로 워드 생성:
   ```bash
   uv run --with python-docx --with markdown python -c "
   import markdown
   from docx import Document
   from docx.shared import Pt
   
   # 마크다운 읽기
   with open('$1', 'r') as f:
       md_content = f.read()
   
   # 워드 문서 생성
   doc = Document()
   # 내용 추가 로직
   doc.save('${1%.md}.docx')
   "
   ```

3. 또는 pandoc 사용 (설치 필요):
   ```bash
   pandoc "$1" -o "${1%.md}.docx"
   ```

4. 성공하면: "✅ 워드 문서 생성 완료: [파일명].docx"
