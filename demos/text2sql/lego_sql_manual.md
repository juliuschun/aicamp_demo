# Lego Database

> Claude Code에게 자연어로 질문하면 SQL을 자동 생성합니다.

---

## 스키마 가이드 (AI 참고용)

### 테이블 & 컬럼 (검증 완료)

| 테이블 | 컬럼 | rows |
|--------|------|------|
| lego_sets | set_num, name, year, theme_id, num_parts | 11,673 |
| lego_themes | id, name, parent_id | 614 |
| lego_parts | part_num, name, part_cat_id | 25,993 |
| lego_colors | id, name, rgb, is_trans | 135 |
| lego_inventories | id, version, set_num | 11,681 |
| lego_inventory_parts | inventory_id, part_num, color_id, quantity, is_spare | 580,251 |
| lego_inventory_sets | inventory_id, set_num, quantity | 2,846 |
| lego_part_categories | id, name | 57 |

### JOIN 패턴 (테스트 완료)

```sql
-- 세트 → 테마
lego_sets.theme_id = lego_themes.id

-- 세트 → 인벤토리 → 부품
lego_sets.set_num = lego_inventories.set_num
lego_inventories.id = lego_inventory_parts.inventory_id

-- 부품 상세
lego_inventory_parts.part_num = lego_parts.part_num
lego_inventory_parts.color_id = lego_colors.id

-- 부품 카테고리
lego_parts.part_cat_id = lego_part_categories.id
```

---

## 질문 예시

- 전체 레고 세트 몇 개야?
- 테마별 세트 수 TOP 10
- 가장 부품 많은 세트 TOP 10
- 연도별 평균 부품 수 변화
- Star Wars 테마 연도별 출시 현황
- 어떤 색상이 가장 많이 쓰여?
