# /workflow - ワークフロー実行・管理

指定されたワークフローを実行・管理するコマンド。
**状態管理機能付き**で、現在のステップを追跡し、差し戻しにも対応。

## 使い方

```
/workflow <workflow-name> <action> [args]
```

### アクション

| アクション | 説明 | 例 |
|------------|------|-----|
| `start` | ワークフローを開始 | `/workflow feature start` |
| `status` | 現在のステップを確認 | `/workflow feature status` |
| `next` | 次のステップへ進む | `/workflow feature next` |
| `back <step>` | 指定ステップに戻る | `/workflow feature back 2` |
| `reset` | ワークフローをリセット | `/workflow feature reset` |

### ワークフロー種別

| 種別 | 定義ファイル | 用途 |
|------|--------------|------|
| `feature` | `.ai/workflows/feature.yml` | 新機能開発 |
| `bugfix` | `.ai/workflows/bugfix.yml` | バグ修正 |
| `schema` | `.ai/workflows/schema-update.yml` | スキーマ更新 |

---

## 実行手順

### `start` アクション

1. **ワークフロー定義を読み込む**
   ```
   .ai/workflows/{workflow-name}.yml
   ```

2. **状態ファイルを作成**
   ```
   .ai/workflow-state.yml
   ```
   テンプレート: `.ai/templates/workflow-state-template.yml`

3. **Step 1から開始**
   - 担当エージェントの設定を読み込む
   - そのロールとして振る舞う
   - ステータスを報告

### `status` アクション

1. **状態ファイルを読み込む**
2. **現在の状態を報告**
   - 現在のステップ
   - 担当エージェント
   - 期待される成果物
   - 次のステップ

### `next` アクション

1. **現在のステップの完了を確認**
   - 成果物が揃っているか
   - Human確認が必要な場合は完了しているか
2. **状態ファイルを更新**
3. **次のステップを開始**

### `back <step>` アクション

1. **指定されたステップに状態を戻す**
2. **差し戻し理由を記録**
3. **そのステップを再開**

---

## 状態ファイル形式

```yaml
# .ai/workflow-state.yml
workflow: feature
current_step: 2
started_at: "2026-01-11T18:00:00+09:00"
task_name: "ユーザー認証機能"

steps_completed:
  - step: 1
    name: "並列設計"
    completed_at: "2026-01-11T18:30:00+09:00"
    outputs:
      - docs/design/auth-design.md
      - docs/design/auth-tasks.md

human_confirmations:
  - step: 2
    question: "認証方式はJWTとセッション、どちらを採用しますか？"
    answer: "JWT"
    confirmed_at: "2026-01-11T19:00:00+09:00"

backtrack_history:
  - from_step: 4
    to_step: 2
    reason: "仕様が不明確だったため"
    timestamp: "2026-01-11T19:30:00+09:00"
```

---

## ステータス報告形式

```
📋 Workflow Status
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

ワークフロー: Feature Development
開始日時: 2026-01-11 18:00
タスク: ユーザー認証機能

━━━ 進捗 ━━━

✅ Step 1: 並列設計 (Architect)
🔄 Step 2: 仕様詳細化 (Spec Writer) ← 現在
⬜ Step 3: 設計レビュー (Reviewer)
⬜ Step 4: TDD実装 (Implementer)
⬜ Step 5: コードレビュー (Reviewer)
⬜ Step 6: テスト実行 (Tester)
⬜ Step 7: PR作成 (Implementer)
⬜ Step 8: Human承認

━━━ 現在のステップ ━━━

担当: Spec Writer (Claude Opus)
アクション: 詳細仕様書を作成、Human確認

期待される成果物:
- [ ] 詳細仕様書
- [ ] テストケース定義（Given-When-Then）
- [ ] Human確認記録

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## 差し戻しルール

| 差し戻し元 | 差し戻し先 | 条件 |
|------------|------------|------|
| Step 3 | Step 1 | 設計レビューでrejected |
| Step 4 | Step 2 | **仕様に不明点がある** |
| Step 5 | Step 4 | コードレビューでrejected |
| Step 6 | Step 4 | テスト失敗 |
| Step 8 | Step 4 | Humanからフィードバック |

---

## 注意事項

1. **エージェントの責務を守る**
   - Implementerは仕様を作らない
   - Spec WriterはHuman確認を省略しない

2. **状態ファイルを常に更新する**
   - ステップ完了時に必ず更新
   - 差し戻し時も記録

3. **Human確認は記録する**
   - 質問と回答を残す
   - 後から参照できるように
