---
description: 데이터를 엑셀 파일로 변환
argument-hint: [파일경로 또는 데이터]
---
$1 내용을 엑셀로 변환해줘:

1. 데이터 형식 파악 (JSON, CSV, 마크다운 테이블 등)
2. Python으로 엑셀 생성:
   ```bash
   uv run --with openpyxl python -c "
   from openpyxl import Workbook
   # 데이터 처리 로직
   wb = Workbook()
   ws = wb.active
   # 데이터 입력
   wb.save('output.xlsx')
   "
   ```

3. 또는 pandas 사용:
   ```bash
   uv run --with pandas --with openpyxl python -c "
   import pandas as pd
   # CSV나 JSON이면
   df = pd.read_csv('$1')  # 또는 pd.read_json('$1')
   df.to_excel('${1%.*}.xlsx', index=False)
   "
   ```

4. 성공하면: "✅ 엑셀 생성 완료: [파일명].xlsx"
