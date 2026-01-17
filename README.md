# 🤖 AI-Driven Development Template

**2026年のAI駆動開発パラダイム**を実践するためのプロジェクトテンプレート。

人間は**オーケストレーター/監督**、AIは**実行者（設計・実装・レビュー・テスト）**として役割分担し、高品質なソフトウェアを効率的に開発します。

---

## 📋 目次

- [クイックスタート](#-クイックスタート)
- [パラダイム：人間とAIの役割分担](#-パラダイム人間とaiの役割分担)
- [ディレクトリ構成](#-ディレクトリ構成)
- [ワークフロー管理](#-ワークフロー管理)
- [要件定義の進め方](#-要件定義の進め方)
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
┌─────────────────────────────────────────────────────────────────┐
│                      Human (Orchestrator)                        │
│  ┌─────────────┬──────────────┬───────────────────────┐         │
│  │ Intent      │ Security     │ Exception             │         │
│  │ Architect   │ Gatekeeper   │ Handler               │         │
│  │ 「なぜ作るか」│ 最終判断     │ AI間の調停            │         │
│  └─────────────┴──────────────┴───────────────────────┘         │
└─────────────────────────────────────────────────────────────────┘
                             ↓ ビジョン・要件
┌─────────────────────────────────────────────────────────────────┐
│                      AI Agents (実行者)                          │
│                                                                  │
│ ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌────────┐ │
│ │Require- │→ │Architect│→ │Spec     │→ │Imple-   │→ │Tester  │ │
│ │ments    │  │(基本設計)│  │Writer   │  │menter   │  │(テスト)│ │
│ │Analyst  │  │         │  │(詳細仕様)│  │(TDD)    │  │        │ │
│ │(要件定義)│  │         │  │         │  │         │  │        │ │
│ └─────────┘  └─────────┘  └─────────┘  └─────────┘  └────────┘ │
│       ↑            ↑            ↑            ↑           ↑      │
│       └────────────┴──────── Reviewer ───────┴───────────┘      │
│                            (レビュー)                            │
└─────────────────────────────────────────────────────────────────┘
```

### 開発フローの全体像

```
Human（ビジョン・原体験）
    ↓
Requirements Analyst（要件整理・構造化）  ← Step 0
    ↓ docs/requirements/
Architect（基本設計・タスク分解）          ← Step 1
    ↓ docs/design/
Spec Writer（詳細仕様・テストケース）      ← Step 2
    ↓ docs/design/specs/
Implementer（TDD実装）                    ← Step 4
    ↓ apps/*/src/, tests/
Tester（テスト実行・品質検証）             ← Step 6
```

> **重要**: 「設計」と「仕様」の違い
> - **基本設計（Architect）**: 全体構造、API設計、タスク分解 → 「何を作るか」
> - **詳細仕様（Spec Writer）**: 入出力定義、エッジケース、テストケース → 「どう作るか」

### エージェントの役割

| エージェント | モデル | 責務 |
|--------------|--------|------|
| **Requirements Analyst** | Opus | 要件整理・構造化、ユーザーストーリー作成（Human主体） |
| **Architect** | Opus | **基本設計**、タスク分解、ADR作成 |
| **Spec Writer** | Opus | **詳細仕様**作成、Human確認、テストケース定義 |
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
│   │   ├── requirements-analyst.yml  # 要件定義担当（Human主体）
│   │   ├── architect.yml          # 基本設計担当
│   │   ├── spec-writer.yml        # 詳細仕様担当
│   │   ├── implementer.yml        # TDD実装担当
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
│   ├── requirements/              # 📋 要件定義
│   │   ├── inputs/                # インプット資料（会議議事録等）
│   │   ├── vision.md              # ビジョン・目標
│   │   ├── user-stories/          # ユーザーストーリー
│   │   └── features/              # 機能要件
│   ├── adr/                       # Architecture Decision Records
│   ├── design/                    # 基本設計書
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
  - step: 0
    name: "要件定義"
    completed_at: "2026-01-11T18:00:00+09:00"
    outputs:
      - docs/requirements/vision.md
      - docs/requirements/user-stories/auth-story.md
  - step: 1
    name: "並列設計"
    completed_at: "2026-01-11T18:30:00+09:00"
    outputs:
      - docs/design/auth-design.md
      - docs/design/auth-tasks.md

# Human確認の記録（重要！）
human_confirmations:
  - step: 0
    question: "MVP機能のスコープは？"
    answer: "ログイン・ログアウトのみ、パスワードリセットは後回し"
    confirmed_at: "2026-01-11T18:00:00+09:00"
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
Step 0: 要件定義（Requirements Analyst）
    │   Humanの原体験・ビジョンを構造化
    │   ユーザーストーリー作成、優先度付け（Human決定）
    │   ⚠️ AIは推測で要件を追加しない
    ↓
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

## 📋 要件定義の進め方

要件定義は**Human主体**で進め、AIは整理・構造化をサポートします。

### インプット資料の準備

要件定義に必要な資料を `docs/requirements/inputs/` に配置します。

```
docs/requirements/
├── inputs/                    # 📥 インプット資料（AIが読み取る）
│   ├── meeting-notes/         # 会議議事録
│   │   └── 2026-01-15-kickoff.md
│   ├── existing-docs/         # 既存の仕様書・企画書
│   │   └── product-concept.md
│   └── user-research/         # ユーザーリサーチ結果
│       └── interview-summary.md
├── vision.md                  # 📤 アウトプット：ビジョン
├── user-stories/              # 📤 アウトプット：ユーザーストーリー
└── features/                  # 📤 アウトプット：機能要件
```

### AIへの指示例

```
/workflow feature start

# Requirements Analystとして以下を実行してください：

1. インプット資料の読み込み
   - docs/requirements/inputs/ 配下の全資料を読み込んでください
   - 特に会議議事録に記載されたステークホルダーの発言に注目

2. ビジョンの整理
   - 「なぜ作るのか」「誰のために」「何を解決するか」を抽出
   - 成功指標（KPI）の候補を提示
   - docs/requirements/vision.md に出力

3. ユーザーストーリーの作成
   - As a / I want / So that 形式で整理
   - 私が優先度を判断するので、全ストーリーを列挙してください
   - docs/requirements/user-stories/ に出力

4. 不明点の確認
   - 資料から読み取れない部分は推測せず、質問してください
```

### インプット資料のフォーマット

AIが読み取りやすいフォーマットで準備します：

| 資料タイプ | 推奨フォーマット | 例 |
|-----------|-----------------|-----|
| 会議議事録 | Markdown | 発言者と内容を明記、決定事項を明示 |
| 企画書 | Markdown / PDF | 目的・背景・ターゲットを明記 |
| ユーザーリサーチ | Markdown | 課題・ニーズを箇条書き |
| 競合分析 | Markdown表形式 | 比較軸と各社の特徴 |

**議事録の例**:

```markdown
# キックオフMTG 議事録
日時: 2026-01-15
参加者: PM田中、Eng佐藤、Design鈴木

## 議題

### 1. プロジェクトの背景
- PM田中: 「リモートワーカーの孤独感を解消したい」
- Design鈴木: 「ポストペットのような"育てる"要素を入れたい」

### 2. ターゲットユーザー
- 30-40代のリモートワーカー
- 一人暮らし、または日中一人の時間が長い

## 決定事項
- [ ] ターゲットは「リモートワーカー」に絞る
- [ ] MVP機能: 音声会話のみ

## 保留事項
- キャラクターのビジュアルデザイン方針
```

### 要件定義の完了条件

以下が揃った時点で、次のステップ（基本設計）に進みます：

- [ ] `docs/requirements/vision.md` が作成され、Human承認済み
- [ ] ユーザーストーリーにMoSCoW優先度が付与済み
- [ ] 機能要件がビジョン・ストーリーと紐付いている
- [ ] 不明点・保留事項がリスト化されている

---

## 📝 仕様の書き方

### 仕様を書く場所

| 種別 | 場所 | 担当 |
|------|------|------|
| **要件（インプット）** | `docs/requirements/inputs/` | Human |
| **要件（アウトプット）** | `docs/requirements/` | Requirements Analyst |
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
