# Cleave HHJP ビルドガイド残レビュー

レビュー日: 2026-05-21
対象: [build-guide/README.md](build-guide/README.md), [build-guide/01-ASSEMBLY.md](build-guide/01-ASSEMBLY.md), [build-guide/02-FIRMWARE.md](build-guide/02-FIRMWARE.md), [build-guide/03-CASE.md](build-guide/03-CASE.md), [build-guide/04-USAGE.md](build-guide/04-USAGE.md), [build-guide/05-CUSTOMIZE.md](build-guide/05-CUSTOMIZE.md)

## 現在の状態

致命的だった情報構造の問題は解消済みです。標準ルートは [build-guide/README.md](build-guide/README.md) に定義され、ケースなし状態は動作確認用として整理されました。バッテリー接続の安全情報も [build-guide/01-ASSEMBLY.md](build-guide/01-ASSEMBLY.md) と [build-guide/03-CASE.md](build-guide/03-CASE.md) の両方で接続前に確認できる状態です。

また、以下は [todo.md](todo.md) に移管済みのため、このレビューの残課題からは除外します。

- [build-guide/01-ASSEMBLY.md](build-guide/01-ASSEMBLY.md) のショットキーダイオード左右別写真
- [build-guide/01-ASSEMBLY.md](build-guide/01-ASSEMBLY.md) の空の「テスターを使った導通テスト」章
- [build-guide/README.md](build-guide/README.md) 配下の写真サイズと向きの整理

## 解決済み

- [build-guide/04-USAGE.md](build-guide/04-USAGE.md) に、完成後確認でうまくいかない場合の参照先が追加されました。
- [build-guide/04-USAGE.md](build-guide/04-USAGE.md) に、Bluetoothプロファイルのクリア、`settings_reset`、ZMK Studio Restore Stock Settings の使い分けが追加されました。
- [build-guide/04-USAGE.md](build-guide/04-USAGE.md) の「キーマップ変更」は、ZMK Studio とフォーク変更の入口として補強されました。
- [build-guide/03-CASE.md](build-guide/03-CASE.md) に、3Dプリント素材の初回向け推奨が追加されました。
- [build-guide/03-CASE.md](build-guide/03-CASE.md) のケース構成図には、日本語対応表が追加されました。
- [build-guide/05-CUSTOMIZE.md](build-guide/05-CUSTOMIZE.md) に、初期キーマップのまま使う場合は必須ではないことが追加されました。
- 重要な一部画像には、代替テキストが追加されました。

## 残る改善候補

### 1. 用語表記の全体統一

今回触った範囲では `基板`、`はんだ付け` の表記を整えましたが、全体としてはまだ表記ゆれが残る可能性があります。

候補:

- `オモテ面` / `表面` / `背面`
- `Bluetooth接続` / `Bluetooth 接続`
- `USB接続` / `USB 接続`

一括置換だけで処理すると文脈を壊す可能性があるため、章単位で確認しながら統一するのがよさそうです。

### 2. コールアウトの使い分け基準

NOTE、TIP、IMPORTANT、CAUTION、WARNING の使い分けはおおむね読めますが、全体ルールはまだ明文化されていません。

推奨する整理:

- `WARNING` / `CAUTION`: LiPo、ショート、破損、発火など安全上の注意
- `IMPORTANT`: 向き、左右差、設定消失など作業成否に関わる注意
- `NOTE`: 補足
- `TIP`: おすすめ、コツ、選び方

現時点で致命的な混乱はありませんが、公開前の仕上げとして確認すると手順書の信頼感が上がります。

### 3. 完了チェックの粒度

完了チェックは有効ですが、必須、任意、構成依存の項目が混ざっている箇所があります。

例:

- MX構成でのみ必要なスタビライザー
- インジケーター穴ありトップカバーを選んだ場合のみ必要な透明プラ棒
- 滑り止めなど好みに応じて実施する項目

構成依存の項目には `（MX構成のみ）`、`（インジケーター穴ありの場合）`、`（任意）` のようなラベルを付けると、読者が自分の構成に合わせて判断しやすくなります。

### 4. 「完成状態」は作業ステップから分けてもよい

[build-guide/03-CASE.md](build-guide/03-CASE.md) の「13. 完成状態」は、実作業というより完成例の写真です。番号付きステップに含めると、まだ作業があるようにも見えます。

修正するなら、`参考: 完成状態` または `完成状態の確認` のように、作業ステップと区別するとよさそうです。

### 5. 重要画像以外の代替テキストとキャプション

今回、手順成否に関わる一部画像には代替テキストを追加しました。ただし、全画像に網羅的な代替テキストやキャプションを付けたわけではありません。

今後の仕上げとしては、次の順で追加すると効果的です。

1. 部品の向き、固定位置、極性など作業成否に関わる画像
2. 完成状態や比較用の画像
3. 補助的な雰囲気写真

写真サイズや向きの統一は [todo.md](todo.md) に移管済みなので、この項目とは分けて扱います。

### 6. root README と build-guide README の役割

[README.md](README.md) と [build-guide/README.md](build-guide/README.md) はどちらもドキュメント入口です。現状でも大きな問題はありませんが、将来的には root 側をプロジェクト全体の入口、build-guide 側を購入者向けのビルド入口としてより明確に分ける余地があります。

## 次に着手するなら

次の小さな改善としては、以下の順がおすすめです。

1. 完了チェックの構成依存ラベルを付ける
2. [build-guide/03-CASE.md](build-guide/03-CASE.md) の「13. 完成状態」を参考セクションにする
3. 用語表記の全体チェックを行う
4. コールアウトの使い分けを全体確認する
5. 重要度順に残り画像の代替テキストやキャプションを追加する
