# コンポーネント仕様：KYCステータスバー（kyc_status_bar.dart）

## 1. 概要

- eKYC の審査状況を表示するためのステータスバー
- ユーザーの本人確認フローが完了していない状態で、`home`画面の上部に表示される

## 2. 主な役割

- eKYC 審査中であることをユーザーに明示し、処理の進行状況を理解してもらう
- ステータスによってメッセージや見た目を動的に変化させる

## 3. 表示仕様

| ステータス | 表示内容例 | 表示色 | 備考 |
|------------|-------------|---------|------|
| `under_review` | ご本人確認の審査中です（最大24時間かかります） | ブルー | 初回提出後～審査中 |
| `rejected` | ご本人確認に失敗しました。再提出をお願いします。 | レッド | 再提出導線あり |
| `approved` | 表示なし | 表示なし | KYC完了後は非表示 |

## 4. 実装仕様

- 使用ファイル：`lib/components/kyc_status_bar.dart`
- 外部から受け取る状態：`kycStatusProvider`（Riverpod等）
- 表示条件：ステータスが `approved` 以外のときに表示
- メッセージとスタイルは `when` 文等で切り替え

```dart
final kycStatus = ref.watch(kycStatusProvider);

Widget build(BuildContext context) {
  if (kycStatus == 'approved') return SizedBox.shrink();

  Color bgColor = kycStatus == 'under_review' ? Colors.blue[50]! : Colors.red[50]!;
  String message = kycStatus == 'under_review'
      ? 'ご本人確認の審査中です（最大24時間かかります）'
      : 'ご本人確認に失敗しました。再提出をお願いします。';

  return Container(
    color: bgColor,
    padding: EdgeInsets.all(12),
    child: Row(
      children: [
        Icon(Icons.info_outline, size: 18),
        SizedBox(width: 8),
        Expanded(child: Text(message)),
      ],
    ),
  );
}
```

## 5. テスト観点

- ステータスが `under_review` のときに正しくメッセージが表示されるか
- ステータスが `rejected` のときにメッセージ・色が変化するか
- `approved` のときは完全に非表示になるか
- 再提出導線の表示（必要に応じてボタン表示）

## 6. 備考

- ステータスはバックエンドから定期取得 or ログイン時に取得し、永続化しておくことが望ましい
- 表示位置は `home`画面の上部、カード表示の直前を想定
- 状態更新は `kycStatusProvider` を中心に全体連携
