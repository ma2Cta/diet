# 週次献立作成 + 買い物リスト生成

引数: 週開始日（例: `2026-03-02`）

対象週: $ARGUMENTS

## 実行手順

1. `data/equipment/hotcook_kn-hw10g-b.md` を読み、容量制約を確認する。
2. `data/recipes/recipe_index.csv` と `data/recipes/recipe_ingredients.csv` を読み、使用可能レシピを把握する。
3. `data/pantry/pantry_stock.csv` と `data/pantry/pantry_exclude.csv` を読み、在庫・除外品を確認する。
4. `plans/base/` を参照し、食事戦略（低脂質・高タンパク・ご飯200g・朝は調理なし・ホットクック週2回）に沿った献立を作成する。
5. `plans/weekly/$ARGUMENTS/meal_plan.csv` を新規作成または更新する。
   - 全 `dish_id` が `recipe_index.csv` に存在することを確認する。
6. 献立と在庫を元に `shopping/generated/$ARGUMENTS_shopping.csv` を生成する。
   - `shopping/orders/` の確定ファイルは上書きしない。
7. 変更ファイルとコミットメッセージをユーザーに提示し、承認を得てからコミットする。
   - コミットメッセージ例: `plan: add meal plan for $ARGUMENTS` + `shopping: generate list for $ARGUMENTS`

## 完了後の出力

- 作成した献立の概要（朝/昼/夕 × 7日）
- 買い物リストの品目数と主な購入品
- 次アクション提案（発注確定など）
