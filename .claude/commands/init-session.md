# /init-session - AIオーケストレーション初期化

このコマンドは、AI駆動開発のコンテキストをロードします。

## 実行手順

1. **オーケストレーター読み込み**
   - `.ai/orchestrator.yml` を読み込む
   - プロジェクトのAI役割分担を確認

2. **タスク種別の確認**
   ユーザーに以下を確認：
   - 「現在のタスクは何ですか？」
   - 選択肢: Feature（新機能開発）/ Bugfix（バグ修正）/ Schema Update（スキーマ更新）/ その他

3. **ワークフロー読み込み**
   回答に応じて適切なワークフローファイルをロード：
   - Feature → `.ai/workflows/feature.yml`
   - Bugfix → `.ai/workflows/bugfix.yml`
   - Schema Update → `.ai/workflows/schema-update.yml`

4. **初期エージェント起動**
   ワークフローの最初のステップに対応するエージェント定義を読み込み：
   - 通常は `.ai/agents/architect.yml`（Architectから開始）
   - そのロールのpersonaを適用

5. **状態宣言**
   以下の形式でユーザーに現在状態を報告：

   ```
   🤖 AI Orchestration Initialized

   ワークフロー: [Feature/Bugfix/etc]
   現在のステップ: [ステップ名]
   現在のロール: [Architect/Implementer/etc]

   次のアクション: [ワークフロー定義に基づく次のアクション]
   ```

## 注意事項

- セッション途中でもこのコマンドを実行可能（状態リセット）
- ワークフロー変更時は明示的に `/init-session` を再実行すること
