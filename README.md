# 🤖 AI-Driven Development Template

**2026年のAI駆動開発パラダイム**を実践するためのプロジェクトテンプレート。

人間は**オーケストレーター/監督**、AIは**実行者（設計・実装・レビュー・テスト）**として役割分担し、高品質なソフトウェアを効率的に開発します。

---

## 📋 目次

- [クイックスタート](#-クイックスタート)
- [パラダイム：人間とAIの役割分担](#-パラダイム人間とaiの役割分担)
- [ディレクトリ構成](#-ディレクトリ構成)
- [ワークフロー管理](#-ワークフロー管理)
- [仕様の書き方](#-仕様の書き方)
- [推奨MCP（Model Context Protocol）](#-推奨mcpmodel-context-protocol)
- [コマンド一覧](#-コマンド一覧)
- [カスタマイズ](#-カスタマイズ)

---

## 🚀 クイックスタート

### 1. テンプレートをコピー

```bash
# GitHubの "Use this template" ボタンを使うか、手動でクローン
git clone https://github.com/yusuke-kato/ai-driven-dev-template.git my-project
cd my-project
rm -rf .git
git init
```

### 2. プロジェクト名を設定

```bash
# .ai/orchestrator.yml のプロジェクト名を変更
sed -i '' 's/your-project-name/my-project/g' .ai/orchestrator.yml
```

### 3. CLAUDE.md をカスタマイズ

`.claude/CLAUDE.md` を開き、以下を編集：
- プロジェクト概要
- 技術スタック
- チーム構成（必要に応じて）

### 4. Claude Codeでセッション開始

```bash
# Claude Codeを起動
claude

# セッション初期化
/init-session
```

### 5. ワークフロー開始

```
# 機能開発を開始
/workflow feature start

# ワークフローの状態確認
/workflow feature status
```

---

## 🎯 パラダイム：人間とAIの役割分担

```
┌─────────────────────────────────────────────────────────┐
│                    Human (Orchestrator)                  │
│  ┌─────────────┬──────────────┬───────────────────────┐ │
│  │ Intent      │ Security     │ Exception             │ │
│  │ Architect   │ Gatekeeper   │ Handler               │ │
│  │ 「なぜ作るか」│ 最終判断     │ AI間の調停            │ │
│  └─────────────┴──────────────┴───────────────────────┘ │
└─────────────────────────────────────────────────────────┘
                           ↓ 指示
┌─────────────────────────────────────────────────────────┐
│                    AI Agents (実行者)                    │
│                                                          │
│  ┌──────────┐   ┌──────────┐   ┌──────────┐   ┌───────┐│
│  │Architect │ → │Spec      │ → │Implement │ → │Tester ││
│  │(設計)    │   │Writer    │   │er(実装)  │   │       ││
│  │          │   │(仕様詳細)│   │          │   │       ││
│  └──────────┘   └──────────┘   └──────────┘   └───────┘│
│        ↑                           ↑                     │
│        └───────── Reviewer ────────┘                     │
│                  (レビュー)                               │
└─────────────────────────────────────────────────────────┘
```

### エージェントの役割

| エージェント | モデル | 責務 |
|--------------|--------|------|
| **Architect** | Opus | 全体設計、タスク分解、ADR作成 |
| **Spec Writer** | Opus | 詳細仕様作成、Human確認 |
| **Implementer** | Sonnet | TDD実装（仕様に従う、作らない） |
| **Reviewer** | Opus | 設計・コードレビュー、暴走防止 |
| **Tester** | Sonnet | テスト生成・実行、カバレッジ報告 |

---

## 📁 ディレクトリ構成

```
project/
├── .ai/                           # 🤖 AIオーケストレーション
│   ├── orchestrator.yml           # AI間の役割・ワークフロー定義
│   ├── workflow-state.yml         # 現在のワークフロー状態（自動生成）
│   ├── agents/
│   │   ├── architect.yml          # 設計担当
│   │   ├── spec-writer.yml        # 仕様詳細化担当
│   │   ├── implementer.yml        # 実装担当
│   │   ├── reviewer.yml           # レビュー担当
│   │   ├── tester.yml             # テスト担当
│   │   └── subagents/             # 並列サブエージェント
│   │       ├── backend-planner.yml
│   │       ├── mobile-planner.yml
│   │       └── infra-planner.yml
│   ├── workflows/
│   │   ├── feature.yml            # 機能開発フロー
│   │   ├── bugfix.yml             # バグ修正フロー
│   │   └── schema-update.yml      # Schema更新フロー
│   ├── templates/
│   │   ├── workflow-state-template.yml
│   │   └── spec-detail-checklist.md
│   └── memory/                    # プロジェクト固有の長期記憶
│
├── .claude/                       # Claude Code設定
│   ├── CLAUDE.md                  # プロジェクトコンテキスト
│   └── commands/                  # カスタムコマンド
│       ├── init-session.md
│       └── workflow.md
│
├── .security/                     # 人間のみ管理（秘密鍵、監査ルール）
│
├── apps/
│   ├── mobile/                    # モバイルアプリ
│   │   └── .claude/               # Mobile固有コンテキスト
│   ├── server/                    # Backend API
│   │   └── .claude/               # Backend固有コンテキスト
│   └── admin/                     # 管理画面
│       └── .claude/               # Admin固有コンテキスト
│
├── packages/
│   ├── schema/                    # OpenAPI定義（SSOT）
│   └── shared/                    # 共有ユーティリティ
│
├── docs/
│   ├── adr/                       # Architecture Decision Records
│   ├── design/                    # 設計書
│   │   ├── specs/                 # 📝 詳細仕様書
│   │   ├── reviews/               # レビュー結果
│   │   └── decisions/             # 設計判断
│   ├── journal/
│   │   ├── human/                 # 人間の監督ログ
│   │   └── ai/                    # AI自動生成ログ
│   └── understanding/             # 人間向け解説書
│
├── infra/                         # Terraform, Docker
├── e2e/                           # E2Eテスト
└── docker-compose.yml
```

---

## 📊 ワークフロー管理

### workflow-state.yml とは？

ワークフローの**現在の状態**を追跡するファイルです。
どのステップまで完了したか、Human確認の記録、差し戻し履歴などを管理します。

```yaml
# .ai/workflow-state.yml
workflow: feature
current_step: 2
started_at: "2026-01-11T18:00:00+09:00"
task_name: "ユーザー認証機能"

# 完了したステップの記録
steps_completed:
  - step: 1
    name: "並列設計"
    completed_at: "2026-01-11T18:30:00+09:00"
    outputs:
      - docs/design/auth-design.md
      - docs/design/auth-tasks.md

# Human確認の記録（重要！）
human_confirmations:
  - step: 2
    question: "認証方式はJWTとセッション、どちらを採用しますか？"
    answer: "JWT（スケーラビリティを重視）"
    confirmed_at: "2026-01-11T19:00:00+09:00"

# 差し戻し履歴
backtrack_history:
  - from_step: 4
    to_step: 2
    reason: "エラーハンドリングの仕様が不明確だったため"
    timestamp: "2026-01-11T20:30:00+09:00"
```

### Feature開発ワークフロー

```
Step 1: 並列設計（Plan Subagents）
    │   Backend / Mobile / Infra の3つのサブエージェントが並列で設計
    │   Architectが統合、タスクリスト作成
    ↓
Step 2: 仕様詳細化（Spec Writer）
    │   基本設計から詳細仕様を作成
    │   ⚠️ ビジネス判断・UXはHumanに確認（推測しない）
    │   Given-When-Then形式でテストケース定義
    ↓
Step 3: 設計レビュー（Reviewer）
    │   基本設計 + 詳細仕様を統合的にレビュー
    │   → Approved: Step 4へ
    │   → Rejected: Step 1 or 2 へ差し戻し
    ↓
Step 4: TDD実装（Implementer）
    │   ⚠️ 仕様は作らない、従う
    │   RED → GREEN → REFACTOR
    │   不明点はSpec Writerに差し戻し
    ↓
Step 5: コードレビュー（Reviewer）
    │   → Approved: Step 6へ
    │   → Rejected: Step 4へ
    ↓
Step 6: テスト実行（Tester）
    │   全テスト実行、カバレッジ80%以上
    ↓
Step 7: PR作成（Implementer）
    ↓
Step 8: Human承認
    │   最終確認、マージ
    ↓
  DONE 🎉
```

---

## 📝 仕様の書き方

### 仕様を書く場所

| 種別 | 場所 | 担当 |
|------|------|------|
| **基本設計** | `docs/design/*.md` | Architect |
| **詳細仕様** | `docs/design/specs/*.md` | Spec Writer |
| **テストケース** | 詳細仕様書内（Given-When-Then） | Spec Writer |
| **ADR** | `docs/adr/ADR-XXX-*.md` | Architect |

### 詳細仕様のチェックリスト

`.ai/templates/spec-detail-checklist.md` を使って、以下を必ず定義：

```markdown
## 入力
- [ ] データ型
- [ ] バリデーションルール
- [ ] 境界値（最小/最大）
- [ ] デフォルト値

## 出力
- [ ] 正常系レスポンス
- [ ] 異常系レスポンス

## エッジケース
- [ ] null / undefined の扱い
- [ ] 空文字 / 空配列の扱い
- [ ] タイムアウト時の動作

## エラーハンドリング
- [ ] バリデーションエラー
- [ ] 認証エラー
- [ ] 外部サービスエラー

## テストケース（Given-When-Then形式）
```gherkin
Given: ユーザーが未ログイン状態
When: 保護されたAPIにアクセス
Then: 401 Unauthorized が返される
```
```

### Human確認が必須な項目

以下は**推測せず、必ずHumanに確認**：

- ユーザー体験に関わる仕様
- ビジネスロジック・ビジネスルール
- 優先度の判断
- トレードオフの判断

---

## 🔧 推奨MCP（Model Context Protocol）

AI駆動開発をより効果的に行うために、以下のMCPを推奨します。

### 1. claude-mem（長期記憶）

セッションを跨いでプロジェクトの知識を保持。

```json
{
  "mcpServers": {
    "claude-mem": {
      "command": "npx",
      "args": ["-y", "@anthropics/claude-mem-mcp"]
    }
  }
}
```

**用途**:
- 過去の設計決定を記憶
- バグ修正のパターンを学習
- プロジェクト固有の用語・規約を保持

### 2. filesystem MCP（ファイル操作）

ファイルの読み書きを効率化。

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/project"]
    }
  }
}
```

### 3. git MCP（バージョン管理）

Git操作をAIが直接実行。

```json
{
  "mcpServers": {
    "git": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-git"]
    }
  }
}
```

### 4. browser MCP（Webブラウザ操作）

E2Eテストや情報収集に活用。

### MCP設定ファイルの場所

```
~/.config/claude/mcp_settings.json
```

---

## 💻 コマンド一覧

### セッション管理

| コマンド | 説明 |
|----------|------|
| `/init-session` | オーケストレーションコンテキストをロード |

### ワークフロー管理

| コマンド | 説明 |
|----------|------|
| `/workflow feature start` | 機能開発ワークフローを開始 |
| `/workflow bugfix start` | バグ修正ワークフローを開始 |
| `/workflow feature status` | 現在の状態を確認 |
| `/workflow feature next` | 次のステップへ進む |
| `/workflow feature back 2` | Step 2 へ差し戻し |

---

## 🛠 カスタマイズ

### 1. エージェントの追加

`.ai/agents/` に新しいYAMLファイルを作成：

```yaml
# .ai/agents/my-agent.yml
name: MyAgent
provider: claude
model: sonnet

system_prompt: |
  あなたは...

responsibilities:
  - ...

constraints:
  - ...
```

### 2. ワークフローの追加

`.ai/workflows/` に新しいYAMLファイルを作成：

```yaml
# .ai/workflows/my-workflow.yml
name: my-workflow
description: "カスタムワークフロー"

steps:
  - id: 1
    name: "ステップ1"
    agent: architect
    action: |
      - ...
    next: 2
```

### 3. サブエージェントの追加

プロジェクトのドメインに応じて `.ai/agents/subagents/` にサブエージェントを追加：

- `frontend-planner.yml`（Webフロントエンド）
- `ml-planner.yml`（機械学習）
- `security-planner.yml`（セキュリティ）

---

## 📚 参考資料

### 解説記事（Note）

- [2025年のAI開発で学んだことを全部活かして、2026年の「AI駆動開発」を設計した話](https://note.com/yusuke_kato8410/n/n52e998c58555) - パラダイムの設計思想
- [2026年の開発流儀：AIで「実装→レビュー→修正→統合」のループを回す仕組みができた](https://note.com/yusuke_kato8410/n/ne8928eda3e66) - 実践編
- **AI駆動開発テンプレートを公開します** - このテンプレートの詳細解説（公開後追記）

### ドキュメント

- [Claude Code Documentation](https://docs.anthropic.com/claude-code)
- [Model Context Protocol](https://modelcontextprotocol.io/)

---

## 📄 ライセンス

MIT License

---

## 🤝 貢献

Issue、Pull Requestを歓迎します。

このテンプレートを使って開発した経験やフィードバックがあれば、ぜひ共有してください！

---

**Built with ❤️ by AI and Human collaboration**
