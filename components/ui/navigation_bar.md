# ナビゲーションバー仕様（navigation_bar.md）

## 1. 概要

アプリ下部に固定表示される**ボトムナビゲーションバー（Bottom Navigation Bar）**の仕様について記載する。ユーザーが主要な3画面に素早くアクセスできる導線を提供する。

本アプリはカード決済を主機能とするが、拡張性を考慮し、以下3つの画面を常時ナビゲーションバーに配置する方針とする。

---

## 2. 構成

| ラベル名（日本語） | ラベル名（英語） | アイコン例      | 遷移先画面              |
|------------------|------------------|------------------|-------------------------|
| ホーム            | Home             | house / home     | `/home`（カード一覧表示）|
| 利用明細          | Activity         | list / receipt   | `/activity`（決済履歴）  |
| アカウント        | Account          | person / settings| `/account`（設定・情報） |

---

## 3. 表示仕様

- 常に画面下部に表示（全画面共通）
- アクティブ状態のアイコンは色付き、非アクティブはグレースケール
- ラベルテキストは英語表示（日本語ローカライズが必要な場合は切替可能とする）

---

## 4. 実装メモ

- Flutterの`BottomNavigationBar`ウィジェットを使用
- 状態は`Provider`で現在選択中のインデックスを管理
- アイコンは `Icons` または `flutter_svg` 経由でカスタムSVGに差し替え可能

```dart
BottomNavigationBar(
  currentIndex: context.watch<navigationProvider>().currentIndex,
  onTap: (index) => context.read<navigationProvider>().update(index),
  items: const [
    BottomNavigationBarItem(
      icon: Icon(Icons.home),
      label: 'Home',
    ),
    BottomNavigationBarItem(
      icon: Icon(Icons.receipt_long),
      label: 'Activity',
    ),
    BottomNavigationBarItem(
      icon: Icon(Icons.person),
      label: 'Account',
    ),
  ],
);
```

---

## 5. 備考

- 今後の機能追加に応じて項目の追加／再編成を検討（例：チャージ機能、キャンペーン、通知）
- 初期リリースではこの3画面に限定して簡潔なナビゲーション体験を優先する
