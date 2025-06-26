# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

このプロジェクトは、AI開発コラボレーション用の品質確認チェックリストシステムです。YAMLベースでチェックリストを管理し、開発時の品質確認を支援します。

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

2. 重要度（severity）を判断する
   - `critical`: 致命的な問題（セキュリティ脆弱性など）
   - `high`: 重要な問題（機能が動作しないなど）
   - `medium`: 中程度の問題（コード品質、パフォーマンス）
   - `low`: 軽微な問題（ドキュメント不足など）

3. YAMLファイルの該当カテゴリに項目を追加
   - IDは `カテゴリ名_連番` の形式で生成
   - questionフィールドに質問文を設定
   - hintフィールドに確認のヒントを追加

### 追加例

ユーザー: 「環境変数の設定が正しいかチェックする項目を追加して」

AIの対応:
1. カテゴリ: `security` または `requirements`
2. 以下の形式でYAMLに追加：

```yaml
- id: sec_004
  question: "環境変数の設定は正しく行われていますか？"
  severity: high
  hint: ".envファイルの存在、必要な環境変数の定義を確認"
```

## ディレクトリ構造

```
tsumesyo/
├── checklists/        # チェックリストYAMLファイル
│   └── template.yaml  # テンプレートファイル
├── scripts/           # ユーティリティスクリプト
└── docs/             # ドキュメント
```

## 使用方法

1. チェックリストの作成
   - `checklists/template.yaml` をコピーしてプロジェクト用のチェックリストを作成
   - 必要に応じて項目をカスタマイズ

2. AIへの渡し方
   - YAMLファイルの内容をそのままコピー＆ペースト
   - またはファイルパスを指定して読み込み

## 重要な注意事項

- チェックリスト項目を追加する際は、既存の形式に従ってください
- 新しいカテゴリが必要な場合は、categoriesセクションに追加してください
- 自動チェック可能な項目は automated_checks セクションに追加できます