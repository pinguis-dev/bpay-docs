## 画面構成の作成ルール

### 目的

画面ごとの仕様を整理し、設計者・実装者・レビュー担当が共通理解を持てるようにする。

### 管理場所

- `screen_specs/` ディレクトリ配下に、1画面 = 1ファイルで作成
- ファイル名は Flutter の画面ファイル名と対応させる（例：`login.md`）

### ファイル記述フォーマット（Markdown）

以下のテンプレートに沿って記述する：

````markdown
# 画面名：ログイン

## ファイル名
login.md

## 対応するFlutterファイル
lib/screens/login/login_screen.dart

## 機能概要
- ユーザーがログイン情報を入力し、認証処理を行う
- 初回アクセス時は未ログイン状態と想定

## UI要素一覧
| 要素       | 種類       | 説明                      |
|------------|------------|---------------------------|
| メールアドレス | TextField | ユーザーのメールを入力 |
| パスワード   | TextField | パスワードを入力         |
| ログインボタン | Button    | 認証処理を実行           |
| パスワードを忘れた場合 | Link | リセット画面に遷移     |

## 遷移先
- ログイン成功：`/home`
- パスワード再設定：`/reset_password`

## 備考
- 入力バリデーションは `@utils/validators.dart` を利用
````

### 補足ルール

- テーブル形式での記述を推奨（UI要素、遷移先など）
- Flutterでの実装に必要な情報を意識して構成
- ファイル名・ルート名は一貫性を重視すること

---

## コンポーネント設計方針（Flutter）

### 目的

Widget（コンポーネント）を小さく再利用可能に保ち、保守性とテスト性を高める。

### ディレクトリ設計（推奨）

```
lib/
├── screens/           # 単位画面（ページ）
│   ├── login_screen.dart
│   └── home_screen.dart
├── widgets/           # 再利用可能な部品
│   ├── primary_button.dart
│   └── input_field.dart
├── forms/             # 複数入力をまとめたフォーム部品
│   └── login_form.dart
```

### 命名ルール

- 画面（Page）：`*_screen.dart`
- 再利用可能なWidget：`*_widget.dart` または `*_tile.dart`, `*_card.dart`
- フォーム部品：`*_form.dart`
- ステートレス：`StatelessWidget` を基本とするが、状態があるものは `StatefulWidget`

### 設計方針

- 複雑な画面は**機能単位でWidgetに分割**する（例：ヘッダー、入力フォーム、リスト）
- レスポンシブ対応（縦横・タブレット）を前提とした構造を意識する
- イベント処理は可能な限り上位層に記述し、UIコンポーネントはロジックから分離する

### 備考

- コンポーネントの粒度は「再利用可能かどうか」を基準とする
- ビジネスロジックは別ファイル（`*.service.dart` や `*.controller.dart`）に分離

---

## 状態管理の方針（Flutter）

### 目的

アプリ内の状態（ログイン状態、読み込み状態、ユーザー情報など）を一元管理し、画面間での情報共有・制御を適切に行う。

### 初期方針

- 状態管理には `Provider` を基本とする
- 後に要件が複雑になった場合は `Riverpod` への移行も検討する

### 典型的な状態例

| 状態名           | 型            | 用途例                                  |
|------------------|----------------|------------------------------------------|
| currentUser      | `User?`        | ログイン済みユーザーの保持              |
| isLoading        | `bool`         | ボタン押下時のローディング制御          |
| authState        | `enum`         | 未認証／認証済み／期限切れ などを判定   |

### 実装例（Provider）

```dart
class AuthProvider extends ChangeNotifier {
  User? _user;
  bool _loading = false;

  User? get user => _user;
  bool get isLoading => _loading;

  Future<void> login(String email) async {
    _loading = true;
    notifyListeners();

    // 認証処理...
    _user = await AuthService.login(email);

    _loading = false;
    notifyListeners();
  }
}
```

### 補足

- ログイン済みかどうかの判定は `SplashScreen` など起動時に行う
- 画面間の連携が増えてきた段階で状態管理を導入・拡張する
- Provider は `ChangeNotifierProvider` をベースに構成し、責務ごとに Provider を分割
