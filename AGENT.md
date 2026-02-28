# AGENT.md

このファイルは、このリポジトリを **人手編集ほぼなしで AI エージェントが運用するための標準ルール** です。

## 運用モード
- 基本は「AIに依頼 -> AIが編集 -> AIがコミット」。
- 人間は原則として「依頼」と「結果確認」のみを行う。
- 手動編集は例外（緊急時のみ）。

## 目的
- ダイエット運用を継続可能な仕組みとして Git 管理する。
- 体重目標: `120kg -> 110kg -> 100kg未満`。
- 70点運用を継続し、崩れても翌日復帰を優先する。

## 食事戦略（固定前提）
- 1日3食。
- 朝は調理なし。
- 1回の調理+片付けは20分以内。
- ホットクック週2回まとめ調理。
- ご飯200gを維持（極端な糖質カットをしない）。
- 低脂質・適正炭水化物・高タンパク。
- 甘味は毎日1枠まで許容（禁止しない）。
- コンビニ/Uberは禁止ではなくルール固定で管理。

## ディレクトリ責務
- `recipes/`: レシピ本文マスター
- `data/equipment/`: 調理機器の仕様・制約（献立作成時に最優先で参照）
- `data/recipes/`: 集計用レシピCSV（ID、材料、使用量）
- `data/pantry/`: 在庫・除外品CSV
- `plans/weekly/<YYYY-MM-DD>/meal_plan.csv`: 週次献立の正本（週開始日を使う）
- `plans/base/`: 戦略・固定運用プラン（参照用）
- `shopping/generated/`: 生成物（献立+在庫から算出）
- `shopping/orders/`: 発注確定CSV（AI発注入力）
- `shopping/templates/`: 手動確認用テンプレート（初期たたき台）
- `logs/daily/`: 日次詳細ログ（1日1ファイル）
- `logs/daily_log.csv`: 日次サマリー一覧
- `logs/weekly/<YYYY-MM-DD>.md`: 週次振り返りの履歴正本（週開始日を使う）
- `logs/weekly_summary.md`: 最新週のサマリー（要約）

## SSOT（正本）
1. 献立の正本は `meal_plan.csv`。
2. 買い物数量の算出元は `meal_plan.csv` + `recipe_ingredients.csv` + `pantry_stock.csv`。
3. `shopping/generated/*.csv` は再生成可能ファイルとして扱う（AIが更新）。
4. 最終発注は `shopping/orders/*_order_final.csv` を正とする。
5. 週次振り返りの正本は `logs/weekly/<YYYY-MM-DD>.md`。

## 単位正規化ルール
1. CSVの`unit`は次のみ使用: `g`, `ml`, `個`, `パック`, `缶`, `本`。
2. `recipe_ingredients.csv` は可能な限り `g` / `ml` を優先する。
3. `小さじ` / `大さじ` は登録しない。必要なら換算して登録する。
4. 同一`ingredient`は同一`unit`で統一する。

## 在庫更新ルール
1. 発注確定後に `data/pantry/pantry_stock.csv` を更新する。
2. 週次献立の作成時に、前週残在庫を反映して不足分のみ買う。
3. `pantry_exclude.csv` は「常備で買い物計算から除外する品目」のみを記載する。
4. 在庫が不明な品目は `qty=0` で扱い、買い物対象に含める。

## AI 実行ルール
1. 依頼を受けたら、関連ファイルを読み、必要最小限の差分で更新する。
2. 日付・対象週開始日は必ず明示（例: `2026-03-01`, `2026-03-02`）。
3. CSV は列順を維持し、既存ヘッダを変更しない。
4. 不明点があっても、既存ルールに沿う合理的な仮定で先に作業する。
5. 変更後は簡潔なサマリを返し、必要なら次アクションを1-3件提案する。
6. コミット依頼がある場合は、関連ファイルのみを1目的1コミットでまとめる。
7. `meal_plan.csv` に出現する `dish_id` は必ず `recipe_index.csv` に存在させる。
8. `generated` 更新時は `orders` を上書きしない（確定値を保護）。
9. HotCookメニューを扱う場合は `data/equipment/hotcook_kn-hw10g-b.md` を参照し、容量制約を超えない分量にする。

## 標準タスク（AIにそのまま依頼可能）
- 週次献立作成:
  - `plans/weekly/<YYYY-MM-DD>/meal_plan.csv` を新規作成または更新。
- 買い物リスト生成:
  - 献立と在庫を反映して `shopping/generated/<YYYY-MM-DD>_shopping.csv` を更新。
- 発注確定:
  - `shopping/orders/<YYYY-MM-DD>_order_final.csv` を更新。
- 日次記録:
  - `logs/daily/YYYY-MM-DD.md` を作成し、`logs/daily_log.csv` に追記。
- 週次振り返り:
  - `logs/weekly/<YYYY-MM-DD>.md` を作成/更新し、`logs/weekly_summary.md` も更新。

## 依頼テンプレート（短文でOK）
- `2026-03-09 の週献立を作成してコミットして`
- `2026-03-09 の献立と在庫から買い物リスト生成して`
- `2026-03-03 の食事ログを反映して`
- `今週の週次サマリーを更新してコミットして`

## Git 運用（AIコミット前提）
- 原則: 1目的1コミット。
- `shopping/generated/` はコミット対象（再現性と差分確認のため）。
- `shopping/orders/` は手動確認後に確定コミット。
- 推奨コミット種別:
  - `plan: ...`
  - `shopping: ...`
  - `order: ...`
  - `log: ...`
  - `docs: ...`
- 例:
  - `plan: add meal plan for 2026-03-09`
  - `shopping: regenerate list for 2026-03-09`
  - `log: add daily entry 2026-03-03`

## ルール参照
- 行動ルールは `rules/*.md` を参照する。
- 既存戦略と矛盾する変更を行う場合は、先に `AGENT.md` を更新してから他ファイルを修正する。

## 変更前チェック（AI用）
1. `meal_plan.csv` の全 `dish_id` が `recipe_index.csv` に存在する。
2. `recipe_ingredients.csv` の `unit` が正規化ルール内にある。
3. `pantry_stock.csv` と `pantry_exclude.csv` の重複品目がない。
4. `generated` と `orders` の週番号が一致している。
5. 日次ログ更新時、`daily/YYYY-MM-DD.md` と `daily_log.csv` の日付が一致している。
