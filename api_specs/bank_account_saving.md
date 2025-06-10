# API仕様：銀行口座登録・更新（bank_account_saving.md）

## 1. 概要

ユーザーの銀行口座情報を登録・更新するための仕様を定義します。  
国ごとに管理するテーブル（例：`jp_bank_accounts`、`us_bank_accounts` など）に対し、ユーザーの居住国に応じて保存・切り替え処理を行います。

---

## 2. 対象テーブルと構成

| 居住国 | テーブル名             | 主なカラム例 |
|--------|------------------------|--------------|
| 日本   | `jp_bank_accounts`     | user_id, bank_code, branch_code, account_type, account_number, account_holder_name |
| アメリカ | `us_bank_accounts`   | user_id, routing_number, account_number, account_holder_name |
| 中国   | `cn_bank_accounts`     | user_id, bank_name, province, city, account_number, account_holder_name |
| 韓国   | `kr_bank_accounts`     | user_id, bank_code, account_number, account_holder_name |

---

## 3. 処理仕様

### 3.1 新規登録

- ユーザーの居住国情報を基に、対応する銀行口座テーブルに新規レコードを作成
- 該当テーブルに既存レコードが存在する場合はエラーを返す

### 3.2 変更登録（国変更）

- 現在の居住国と異なる国に口座情報を登録する場合、以下の処理を実行：
  1. 既存の銀行口座テーブルのレコードを削除
  2. 居住国情報をユーザーテーブルに更新
  3. 新しい国のテーブルに銀行口座情報を追加

### 3.3 更新（同一国）

- 居住国に変更がない場合、対応テーブルの該当レコードを更新

---

## 4. エンドポイント例

| メソッド | エンドポイント              | 用途       |
|----------|-----------------------------|------------|
| POST     | `/api/bank_accounts`        | 新規登録    |
| PUT      | `/api/bank_accounts`        | 更新（同国）|
| PUT      | `/api/bank_accounts/change_country` | 国変更を伴う更新 |

---

## 5. バリデーションルール（例）

※詳細は各国仕様書を参照

- account_number：国ごとに桁数・形式が異なる
- bank_code / routing_number：正規コード形式をチェック
- account_holder_name：全角 or 半角チェック、氏名順序

---

## 6. セキュリティ・保存形式

- 機密情報（口座番号、氏名等）は暗号化して保存（例：AES）
- ユーザーIDでユニーク制約を設け、1ユーザー1口座制限を基本とする

---

## 7. 備考

- 居住国は `users` テーブルの `residence_country_code` カラムで管理（例：'JP', 'US'）
- 保存後、最新情報は `bankAccountProvider` を通じてアプリ内に反映される