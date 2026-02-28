# 買い物リスト再生成

引数: 週開始日（例: `2026-03-02`）

## 日付の確定（最初に必ず行う）

$ARGUMENTS が空の場合:
1. 今日の日付から直近の日曜日（当日が日曜なら当日、それ以外なら次の日曜）を `YYYY-MM-DD` 形式で計算する。
2. 以下の形式でユーザーに提示し、回答を待つ。作業は回答後に開始する。

```
対象週の開始日を確認します。

サジェスト: {計算した日曜日の日付}

この日付で進めますか？別の日付を使う場合はそのまま入力してください。
（例: 2026-03-02）
```

3. ユーザーが確認または日付を入力したら、その日付を以降の `$ARGUMENTS` として使用する。

$ARGUMENTS が入力済みの場合: その値をそのまま使用する。

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
