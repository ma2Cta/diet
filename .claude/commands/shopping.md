# 買い物リスト再生成

引数: 週開始日（例: `2026-03-02`）

対象週: $ARGUMENTS

## 実行手順

1. `plans/weekly/$ARGUMENTS/meal_plan.csv` を読む。
2. `data/recipes/recipe_ingredients.csv` を読む。
3. `data/pantry/pantry_stock.csv` と `data/pantry/pantry_exclude.csv` を読む。
4. 不足分を算出し `shopping/generated/$ARGUMENTS_shopping.csv` を更新する。
   - `shopping/orders/` の確定ファイルは上書きしない。
5. 変更ファイルとコミットメッセージをユーザーに提示し、承認を得てからコミットする。
   - コミットメッセージ例: `shopping: regenerate list for $ARGUMENTS`

## 完了後の出力

- 品目数と主な購入品の一覧
- 在庫で補えた品目（省略分）
- 次アクション提案（発注確定など）
