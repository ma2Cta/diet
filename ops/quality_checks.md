# Quality Checks (AI運用前提)

## 献立更新時
- `meal_plan.csv` の `dish_id` が `recipe_index.csv` にすべて存在する。
- `date` は `YYYY-MM-DD` 形式。
- 同日同meal_typeの重複行がない。

## 買い物生成時
- 参照元は `meal_plan.csv` + `recipe_ingredients.csv` + `pantry_stock.csv`。
- `unit` は `g, ml, 個, パック, 缶, 本` のみ。
- `generated` と `orders` で対象週開始日（`YYYY-MM-DD`）が一致している。

## 日次ログ更新時
- `logs/daily/YYYY-MM-DD.md` を作成または更新。
- `logs/daily_log.csv` に同日行を追加または更新。
