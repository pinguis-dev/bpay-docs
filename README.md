<<<<<<< HEAD
# bpay-docs
=======
# bpay ドキュメントリポジトリ

このリポジトリは、bpay（Borderless Payment Service）の設計・仕様・画面遷移・API 定義などを記録・共有するためのドキュメント集です。

---

## ディレクトリ構成

```
bpay-docs/
├── README.md              # このリポジトリの概要
├── screen_flow.md         # Mermaidによる画面遷移図
├── screen_specs/          # 各画面のUI仕様（Markdown形式）
│   ├── login.md
│   ├── home.md
├── api_specs/             # バックエンドAPI仕様（OpenAPI風）
│   ├── auth_api.md
│   ├── payment_api.md
```

---

## このドキュメントの目的

- 国際間決済アプリ「bpay」の設計仕様を整理・共有する
- フロントエンド・バックエンド共通の理解基盤を構築する
- ChatGPTによる自動化・コード生成への活用

---

## 外部リンク

- Figmaデザイン: [リンクをここに追加]
- Notion仕様書ビュー: [リンクをここに追加]
- GitHub - フロントエンド: [リンクをここに追加]
- GitHub - バックエンド: [リンクをここに追加]

---

## 編集ルール（推奨）

- Markdown形式で記述（`.md`）
- ファイル名は `login.md` など画面ファイル名と一致させる
- Mermaid記法で遷移図・構造図を書く
- コードブロックは適宜挿入（Flutter/Reactなど）

---

## 現時点の進行フロー（2025年6月時点）

1. `bpay-docs` リポジトリ作成 & 初期構成準備（完了）
2. `screen_specs/` ディレクトリで各画面の仕様をMarkdown形式で作成
3. `screen_flow.md` にて画面遷移図をMermaid形式で記述
4. 認証方式の決定：Magic Linkによるメールアドレス認証に決定
5. 画面単位で仕様を順に作成中（現在：`login.md`, `home.md`）
6. 必要に応じて命名規則・設計方針を `conventions.md` にまとめ中
7. 一通りの画面仕様が整った後、共通Widgetの抽出・再利用設計に進む予定
>>>>>>> 9511038 (Add initial screen and component spec documents)
