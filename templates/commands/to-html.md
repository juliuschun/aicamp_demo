---
description: 마크다운 파일을 HTML로 변환 (브라우저에서 보기 좋게)
argument-hint: [파일경로]
---
$1 파일을 HTML로 변환해줘:

**방법 1**: grip 사용 (GitHub 스타일)
```bash
uv run --with grip grip "$1" --export "${1%.md}.html"
```

**방법 2**: 직접 변환
1. 파일 내용 읽기
2. 다음 HTML 템플릿으로 감싸기:

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>[제목]</title>
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
            max-width: 800px;
            margin: 0 auto;
            padding: 40px 20px;
            line-height: 1.6;
            color: #333;
        }
        h1, h2, h3 { color: #1a1a1a; }
        code { background: #f4f4f4; padding: 2px 6px; border-radius: 3px; }
        pre { background: #f4f4f4; padding: 16px; border-radius: 6px; overflow-x: auto; }
        blockquote { border-left: 4px solid #ddd; margin-left: 0; padding-left: 16px; color: #666; }
        table { border-collapse: collapse; width: 100%; }
        th, td { border: 1px solid #ddd; padding: 8px 12px; text-align: left; }
        th { background: #f4f4f4; }
    </style>
</head>
<body>
[마크다운을 HTML로 변환한 내용]
</body>
</html>
```

3. 파일로 저장: `[원본파일명].html`
4. "✅ HTML 생성 완료: [파일명].html - 브라우저에서 열어보세요"
