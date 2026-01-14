# Lego Database SQL Manual

> AI 캠프 실습용 레고 데이터베이스 쿼리 매뉴얼

---

## 데이터베이스 개요

| 테이블 | 설명 | 행 수 |
|--------|------|-------|
| lego_sets | 레고 세트 정보 | 11,673 |
| lego_themes | 테마 (Star Wars, City 등) | 614 |
| lego_parts | 개별 부품 | ~50,000 |
| lego_colors | 레고 색상 | ~200 |
| lego_inventories | 세트별 인벤토리 | ~20,000 |
| lego_inventory_parts | 인벤토리-부품 매핑 | ~580,000 |
| lego_part_categories | 부품 카테고리 | ~60 |

---

## 스키마 다이어그램

```
lego_themes (id, name, parent_id)
     │
     └──< lego_sets (set_num, name, year, theme_id, num_parts)
              │
              └──< lego_inventories (id, version, set_num)
                        │
                        └──< lego_inventory_parts (inventory_id, part_num, color_id, quantity, is_spare)
                                   │                      │
                                   │                      └──> lego_colors (id, name, rgb, is_trans)
                                   │
                                   └──> lego_parts (part_num, name, part_cat_id)
                                              │
                                              └──> lego_part_categories (id, name)
```

---

## 핵심 쿼리 10선 (테스트 완료)

### Q1: 연도별 레고 세트 출시 현황

최근 20년간 제품 출시 트렌드 분석

```sql
SELECT
    year AS 출시연도,
    COUNT(*) AS 세트수,
    SUM(num_parts) AS 총부품수,
    ROUND(AVG(num_parts), 1) AS 평균부품수
FROM lego_sets
WHERE year >= 1998
GROUP BY year
ORDER BY year DESC
LIMIT 20;
```

### Q2: 테마별 세트 수 TOP 20

가장 인기 있는 테마 분석

```sql
SELECT
    t.name AS 테마명,
    COUNT(s.set_num) AS 세트수,
    SUM(s.num_parts) AS 총부품수,
    ROUND(AVG(s.num_parts), 1) AS 평균부품수
FROM lego_sets s
JOIN lego_themes t ON s.theme_id = t.id
GROUP BY t.name
ORDER BY 세트수 DESC
LIMIT 20;
```

### Q3: 가장 복잡한 레고 세트 TOP 20

프리미엄 제품 분석 (부품 수 기준)

```sql
SELECT
    s.name AS 세트명,
    t.name AS 테마,
    s.year AS 출시연도,
    s.num_parts AS 부품수
FROM lego_sets s
JOIN lego_themes t ON s.theme_id = t.id
ORDER BY s.num_parts DESC
LIMIT 20;
```

### Q4: 색상별 사용 빈도 TOP 20

어떤 색상이 가장 많이 사용되는가?

```sql
SELECT
    c.name AS 색상명,
    CASE WHEN c.is_trans = 't' THEN '투명' ELSE '불투명' END AS 투명여부,
    COUNT(*) AS 사용횟수,
    SUM(ip.quantity) AS 총수량
FROM lego_inventory_parts ip
JOIN lego_colors c ON ip.color_id = c.id
GROUP BY c.name, c.is_trans
ORDER BY 총수량 DESC
LIMIT 20;
```

### Q5: 부품 카테고리별 부품 종류 수 TOP 20

부품 포트폴리오 분석

```sql
SELECT
    pc.name AS 부품카테고리,
    COUNT(p.part_num) AS 부품종류수
FROM lego_parts p
JOIN lego_part_categories pc ON p.part_cat_id = pc.id
GROUP BY pc.name
ORDER BY 부품종류수 DESC
LIMIT 20;
```

### Q6: Star Wars 테마 연도별 출시 현황

IP 라이선스 제품 분석

```sql
SELECT
    s.year AS 출시연도,
    t.name AS 테마명,
    COUNT(*) AS 세트수,
    SUM(s.num_parts) AS 총부품수,
    MAX(s.num_parts) AS 최대부품수
FROM lego_sets s
JOIN lego_themes t ON s.theme_id = t.id
WHERE t.name LIKE '%Star Wars%'
GROUP BY s.year, t.name
ORDER BY s.year DESC
LIMIT 20;
```

### Q7: 10년 단위 레고 세트 규모 변화

역사적 트렌드 - 복잡도 증가 추세

```sql
SELECT
    (year / 10) * 10 AS 연대,
    COUNT(*) AS 세트수,
    SUM(num_parts) AS 총부품수,
    ROUND(AVG(num_parts), 1) AS 평균부품수,
    MAX(num_parts) AS 최대부품수,
    MIN(CASE WHEN num_parts > 0 THEN num_parts END) AS 최소부품수
FROM lego_sets
WHERE year >= 1950
GROUP BY (year / 10) * 10
ORDER BY 연대;
```

### Q8: 부품 수 구간별 세트 분포

제품 포트폴리오 분석 - 시장 세분화

```sql
SELECT
    CASE
        WHEN num_parts = 0 THEN '0 (부품없음)'
        WHEN num_parts BETWEEN 1 AND 50 THEN '1-50 (소형)'
        WHEN num_parts BETWEEN 51 AND 100 THEN '51-100 (소형)'
        WHEN num_parts BETWEEN 101 AND 200 THEN '101-200 (중소형)'
        WHEN num_parts BETWEEN 201 AND 500 THEN '201-500 (중형)'
        WHEN num_parts BETWEEN 501 AND 1000 THEN '501-1000 (대형)'
        WHEN num_parts BETWEEN 1001 AND 2000 THEN '1001-2000 (초대형)'
        ELSE '2000+ (프리미엄)'
    END AS 부품수구간,
    COUNT(*) AS 세트수,
    ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER(), 1) AS 비율
FROM lego_sets
GROUP BY
    CASE
        WHEN num_parts = 0 THEN '0 (부품없음)'
        WHEN num_parts BETWEEN 1 AND 50 THEN '1-50 (소형)'
        WHEN num_parts BETWEEN 51 AND 100 THEN '51-100 (소형)'
        WHEN num_parts BETWEEN 101 AND 200 THEN '101-200 (중소형)'
        WHEN num_parts BETWEEN 201 AND 500 THEN '201-500 (중형)'
        WHEN num_parts BETWEEN 501 AND 1000 THEN '501-1000 (대형)'
        WHEN num_parts BETWEEN 1001 AND 2000 THEN '1001-2000 (초대형)'
        ELSE '2000+ (프리미엄)'
    END
ORDER BY 세트수 DESC;
```

### Q9: 테마별 평균 부품 수 TOP 20

가장 복잡한 테마 - 프리미엄 라인 식별

```sql
SELECT
    t.name AS 테마명,
    COUNT(*) AS 세트수,
    ROUND(AVG(s.num_parts), 1) AS 평균부품수,
    MAX(s.num_parts) AS 최대부품수,
    MIN(s.year) AS 시작연도,
    MAX(s.year) AS 최종연도
FROM lego_sets s
JOIN lego_themes t ON s.theme_id = t.id
GROUP BY t.name
HAVING COUNT(*) >= 5
ORDER BY 평균부품수 DESC
LIMIT 20;
```

### Q10: 가장 다양한 색상을 사용하는 세트 TOP 20

제품 복잡도 및 시각적 다양성 분석

```sql
SELECT
    s.name AS 세트명,
    t.name AS 테마,
    s.year AS 출시연도,
    s.num_parts AS 부품수,
    COUNT(DISTINCT ip.color_id) AS 사용색상수
FROM lego_sets s
JOIN lego_themes t ON s.theme_id = t.id
JOIN lego_inventories i ON s.set_num = i.set_num
JOIN lego_inventory_parts ip ON i.id = ip.inventory_id
GROUP BY s.set_num, s.name, t.name, s.year, s.num_parts
ORDER BY 사용색상수 DESC
LIMIT 20;
```

---

## 추가 샘플 쿼리 20선

### 기본 조회

```sql
-- 11: 전체 세트 수 확인
SELECT COUNT(*) AS 총세트수 FROM lego_sets;

-- 12: 전체 테마 목록
SELECT name AS 테마명 FROM lego_themes ORDER BY name LIMIT 20;

-- 13: 특정 연도 세트 조회 (2020년)
SELECT name, num_parts FROM lego_sets WHERE year = 2020 ORDER BY num_parts DESC LIMIT 20;

-- 14: 이름에 'Castle' 포함된 세트
SELECT name, year, num_parts FROM lego_sets WHERE name LIKE '%Castle%' ORDER BY year DESC LIMIT 20;

-- 15: 부품 없는 세트 (미니피규어 등)
SELECT name, year, theme_id FROM lego_sets WHERE num_parts = 0 LIMIT 20;
```

### 집계 및 통계

```sql
-- 16: 테마별 최신 출시 세트
SELECT t.name AS 테마, MAX(s.year) AS 최신연도, COUNT(*) AS 총세트수
FROM lego_sets s JOIN lego_themes t ON s.theme_id = t.id
GROUP BY t.name ORDER BY 최신연도 DESC LIMIT 20;

-- 17: 연도별 신규 테마 수
SELECT year, COUNT(DISTINCT theme_id) AS 활성테마수
FROM lego_sets WHERE year >= 2000 GROUP BY year ORDER BY year;

-- 18: 평균 이상 부품 수를 가진 세트
SELECT name, num_parts FROM lego_sets
WHERE num_parts > (SELECT AVG(num_parts) FROM lego_sets)
ORDER BY num_parts DESC LIMIT 20;

-- 19: 테마별 세트 수 vs 평균 부품 수 상관관계
SELECT t.name, COUNT(*) AS 세트수, ROUND(AVG(s.num_parts),0) AS 평균부품
FROM lego_sets s JOIN lego_themes t ON s.theme_id = t.id
GROUP BY t.name HAVING COUNT(*) >= 10 ORDER BY 평균부품 DESC LIMIT 20;

-- 20: 투명 색상 부품 사용 비율
SELECT
    CASE WHEN c.is_trans = 't' THEN '투명' ELSE '불투명' END AS 유형,
    COUNT(*) AS 사용건수,
    SUM(ip.quantity) AS 총수량
FROM lego_inventory_parts ip
JOIN lego_colors c ON ip.color_id = c.id
GROUP BY c.is_trans;
```

### JOIN 활용

```sql
-- 21: 세트와 테마 정보 함께 조회
SELECT s.name AS 세트명, t.name AS 테마명, s.year, s.num_parts
FROM lego_sets s
LEFT JOIN lego_themes t ON s.theme_id = t.id
ORDER BY s.year DESC LIMIT 20;

-- 22: 부품과 카테고리 함께 조회
SELECT p.name AS 부품명, pc.name AS 카테고리
FROM lego_parts p
JOIN lego_part_categories pc ON p.part_cat_id = pc.id
LIMIT 20;

-- 23: 특정 세트의 모든 부품 조회
SELECT p.name AS 부품명, c.name AS 색상, ip.quantity AS 수량
FROM lego_inventory_parts ip
JOIN lego_inventories i ON ip.inventory_id = i.id
JOIN lego_parts p ON ip.part_num = p.part_num
JOIN lego_colors c ON ip.color_id = c.id
WHERE i.set_num = '75192-1'
ORDER BY ip.quantity DESC LIMIT 20;

-- 24: 하위 테마와 상위 테마 함께 조회
SELECT child.name AS 하위테마, parent.name AS 상위테마
FROM lego_themes child
LEFT JOIN lego_themes parent ON child.parent_id = parent.id
WHERE child.parent_id IS NOT NULL
LIMIT 20;

-- 25: 가장 많이 사용된 부품 TOP 20
SELECT p.name AS 부품명, SUM(ip.quantity) AS 총사용량
FROM lego_inventory_parts ip
JOIN lego_parts p ON ip.part_num = p.part_num
GROUP BY p.part_num, p.name
ORDER BY 총사용량 DESC LIMIT 20;
```

### 고급 분석

```sql
-- 26: 연도별 성장률 계산
WITH yearly AS (
    SELECT year, COUNT(*) AS cnt FROM lego_sets GROUP BY year
)
SELECT year, cnt AS 세트수,
    ROUND(100.0 * (cnt - LAG(cnt) OVER (ORDER BY year)) / LAG(cnt) OVER (ORDER BY year), 1) AS 성장률
FROM yearly WHERE year >= 2000 ORDER BY year;

-- 27: 테마별 세트 수 순위
SELECT t.name, COUNT(*) AS 세트수,
    RANK() OVER (ORDER BY COUNT(*) DESC) AS 순위
FROM lego_sets s JOIN lego_themes t ON s.theme_id = t.id
GROUP BY t.name LIMIT 20;

-- 28: 누적 세트 수 (연도별)
SELECT year, COUNT(*) AS 연간출시,
    SUM(COUNT(*)) OVER (ORDER BY year) AS 누적세트수
FROM lego_sets WHERE year >= 2000 GROUP BY year ORDER BY year;

-- 29: 테마별 부품 수 백분위
SELECT t.name AS 테마명, s.num_parts AS 부품수,
    PERCENT_RANK() OVER (ORDER BY s.num_parts) AS 백분위
FROM lego_sets s JOIN lego_themes t ON s.theme_id = t.id
WHERE s.num_parts > 1000 LIMIT 20;

-- 30: 같은 테마 내 세트 간 부품 수 차이
SELECT t.name AS 테마,
    MAX(s.num_parts) - MIN(s.num_parts) AS 부품수차이,
    MAX(s.num_parts) AS 최대, MIN(s.num_parts) AS 최소
FROM lego_sets s JOIN lego_themes t ON s.theme_id = t.id
GROUP BY t.name HAVING COUNT(*) >= 5
ORDER BY 부품수차이 DESC LIMIT 20;
```

---

## SQL 기능별 인덱스

| 기능 | 쿼리 번호 |
|------|-----------|
| SELECT 기본 | 11, 12, 13, 14, 15 |
| COUNT, SUM, AVG | 1, 2, 16, 17 |
| JOIN | 2, 3, 21, 22, 23, 24, 25 |
| GROUP BY | 1, 2, 4, 5, 6, 16, 17, 19 |
| ORDER BY | 모든 쿼리 |
| LIMIT | 모든 쿼리 |
| WHERE 조건 | 6, 13, 14, 15, 18 |
| CASE WHEN | 4, 7, 8 |
| 서브쿼리 | 18 |
| 윈도우 함수 | 8, 26, 27, 28, 29 |
| CTE (WITH) | 26 |
| HAVING | 9, 19, 30 |

---

*Last updated: 2026-01-14*
