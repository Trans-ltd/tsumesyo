# AI開発コラボレーション チェックリスト機能 実装設計書

## 1. 実装方針
### 1.1 技術スタック
- 言語: YAML（データ定義）、Bash（スクリプト）、Markdown（ドキュメント）
- ツール: Claude Code（AI連携）
- ライブラリ: 標準的なYAMLパーサー（各言語で利用可能）

### 1.2 コーディング規約
- YAMLファイル: 2スペースインデント
- 命名規則: snake_case for IDs, 日本語可 for 表示文字列
- ファイル名: ハイフン区切り（kebab-case）

## 2. モジュール構成
```
tsumesyo/
├── checklists/                    # チェックリストファイル
│   ├── template.yaml             # 汎用テンプレート
│   └── [project-name].yaml       # プロジェクト別チェックリスト
├── scripts/                      # ユーティリティスクリプト
│   ├── add_checklist_item.sh    # 項目追加スクリプト
│   └── validate_checklist.sh    # 検証スクリプト
├── docs/                        # ドキュメント
│   ├── features/                # 機能別ドキュメント
│   │   └── ai-checklist/       # この機能のドキュメント
│   └── FEATURE_DEVELOPMENT_FRAMEWORK.md
└── CLAUDE.md                    # AI向け指示書
```

## 3. 詳細実装
### 3.1 チェックリストYAML構造
```yaml
# checklists/template.yaml の実装詳細
metadata:
  name: "開発チェックリスト"
  version: "1.0.0"
  description: "AI開発コラボレーション用の品質確認チェックリスト"
  created_at: "2024-01-01"
  tags:
    - development
    - ai-collaboration
    - quality-check

categories:
  - name: "要件理解"
    id: requirements
    description: "実装が要件を満たしているか確認"
    items:
      - id: req_001  # カテゴリID_連番
        question: "実装内容は要求仕様と一致していますか？"
        severity: high  # critical/high/medium/low
        hint: "元の要求事項と実装を比較してください"
```

### 3.2 AI自動追加機能の実装（CLAUDE.md）
```markdown
## チェックリスト管理

### チェックリスト項目の追加

ユーザーから「〜をチェックリストに追加して」という依頼があった場合は、以下の手順で自動的に追加してください：

1. 適切なカテゴリを判断する
   - `requirements`: 要件に関する項目
   - `code_quality`: コード品質に関する項目
   - `security`: セキュリティに関する項目
   - `testing`: テストに関する項目
   - `performance`: パフォーマンスに関する項目
   - `documentation`: ドキュメントに関する項目
   - `feature_framework`: 新機能開発フレームワーク関連

2. 重要度（severity）を判断する
   - `critical`: 致命的な問題（セキュリティ脆弱性など）
   - `high`: 重要な問題（機能が動作しないなど）
   - `medium`: 中程度の問題（コード品質、パフォーマンス）
   - `low`: 軽微な問題（ドキュメント不足など）

3. YAMLファイルの該当カテゴリに項目を追加
   - IDは `カテゴリ名_連番` の形式で生成
   - questionフィールドに質問文を設定
   - hintフィールドに確認のヒントを追加
```

### 3.3 項目追加スクリプト（参考実装）
```bash
#!/bin/bash
# scripts/add_checklist_item.sh

CHECKLIST_FILE="${1:-checklists/template.yaml}"
CATEGORY="${2}"
QUESTION="${3}"
SEVERITY="${4:-medium}"

# YAMLファイルに項目を追加する処理
# AIが直接YAMLを編集する場合は不要
```

## 4. 設定管理
```yaml
# .claude/settings.local.json での権限設定
{
  "permissions": {
    "allow": [
      "Bash(ls:*)",
      "Bash(find:*)",
      "Bash(git add:*)"
    ],
    "deny": []
  }
}
```

## 5. テスト戦略
### 5.1 YAML構造の検証
```bash
# YAMLファイルの構文チェック
python -c "import yaml; yaml.safe_load(open('checklists/template.yaml'))"
```

### 5.2 AI追加機能のテスト
- テストケース1: 「環境変数の検証をチェックリストに追加して」
  - 期待: securityカテゴリに追加
- テストケース2: 「コードレビューのチェック項目を追加して」
  - 期待: code_qualityカテゴリに追加

### 5.3 使用シナリオテスト
1. 新規プロジェクトでテンプレートをコピー
2. プロジェクト固有の項目を追加
3. 開発中にチェックリストを参照
4. AIに項目追加を依頼

## 6. エラーハンドリング
### 6.1 想定されるエラー
- YAMLファイルの構文エラー
- 存在しないカテゴリへの追加
- 重複するIDの生成

### 6.2 エラー時の対応
- AIは編集前にファイルを読み込んで構造を確認
- 新規カテゴリが必要な場合は作成
- IDの重複を避けるため連番を適切に管理