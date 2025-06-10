# 銀行口座データモデル仕様

## 1. 概要

本アプリでは、銀行口座情報をユーザーの居住国に応じて国別テーブルで管理します。これにより、各国固有の入力仕様やバリデーションロジックに対応しやすくします。

## 2. 管理方針

- ユーザーは常に1つの国の銀行口座情報のみを登録可能。
- 登録された情報は、国別の銀行口座テーブルに保存されます。
- 居住国が変更された場合、以前の銀行口座情報は削除され、新しい国の銀行口座情報が登録されます。
- ユーザーのプロフィール情報には、居住国情報 `residency_country` が保存されます。

## 3. テーブル設計

### ユーザーテーブル（users）

| カラム名             | 型         | 説明                               |
|----------------------|------------|------------------------------------|
| id                   | UUID       | ユーザーID（主キー）               |
| residency_country    | string     | 現在の銀行口座の居住国（JP/US/CN/KR） |

---

### 日本：jp_bank_accounts

| カラム名       | 型       | 備考                   |
|----------------|----------|------------------------|
| user_id        | UUID     | 外部キー（users.id）   |
| bank_name      | string   | 銀行名                 |
| branch_name    | string   | 支店名                 |
| account_type   | enum     | 普通 / 当座            |
| account_number | string   | 7桁数字                |
| account_holder | string   | カタカナ               |

---

### アメリカ：us_bank_accounts

| カラム名         | 型       | 備考                            |
|------------------|----------|---------------------------------|
| user_id          | UUID     | 外部キー（users.id）            |
| bank_name        | string   | 銀行名                          |
| routing_number   | string   | 9桁のABAルーティング番号        |
| account_number   | string   | 8～12桁                         |
| account_holder   | string   | アルファベット表記              |

---

### 中国：cn_bank_accounts

| カラム名         | 型       | 備考                     |
|------------------|----------|--------------------------|
| user_id          | UUID     | 外部キー（users.id）     |
| bank_name        | string   | 銀行名（漢字）            |
| bank_code        | string   | 支店コード               |
| account_number   | string   | 16～19桁                |
| account_holder   | string   | 身分証と一致必須          |

---

### 韓国：kr_bank_accounts

| カラム名         | 型       | 備考                         |
|------------------|----------|------------------------------|
| user_id          | UUID     | 外部キー（users.id）         |
| bank_code        | string   | 3桁の銀行コード              |
| account_number   | string   | 10桁前後                    |
| account_holder   | string   | ハングル                    |

---

## 4. 登録・更新仕様

- ユーザーがフォームで情報を入力・送信した際：
  1. `residency_country` を users テーブルに更新
  2. 該当国の銀行口座テーブルの旧レコードを削除（該当する場合）
  3. 入力された情報を新たに挿入

---

## 5. 技術メモ（Flutter）

- 居住国の変更後は、該当国のフォームUIへ自動で切り替える
- `flutter_secure_storage` には直近入力された値を保持し、次回以降の入力支援に利用
- サーバー側では更新トランザクション内で旧データ削除 → 新データ追加を行う