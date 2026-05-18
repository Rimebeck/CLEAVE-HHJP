# ファームウェアのダウンロード・書き込み・接続確認

[目次に戻る](README.md)

Cleave HHJPはファームウェアをダウンロードし、左右それぞれのXIAO nRF52840 Plusへ書き込み、接続確認を行います。

## 目次

- [ファームウェアのダウンロード](#ファームウェアのダウンロード)
- [ブートローダーモードへの入り方](#ブートローダーモードへの入り方)
- [ファームウェアの書き込み](#ファームウェアの書き込み)
- [接続確認・動作確認](#接続確認動作確認)
- [トラブルシューティング](#トラブルシューティング)

書き込みには、キーボード本体のファームウェアに加えて、Bluetoothペアリング情報や永続化設定を初期化するための`settings_reset`用ファームウェアを使用します。

## ファームウェアのダウンロード

[ファームウェアのダウンロードページ](https://github.com/07JP27/Cleave-HHJP/releases)から最新バージョンの以下のUF2ファイルをダウンロードしてください。

- `settings_reset-seeeduino_xiao_ble-zmk.uf2`
- `cleave_hhjp_left.rgbled_adapter-seeeduino_xiao_ble-zmk.uf2`
- `cleave_hhjp_right.rgbled_adapter-seeeduino_xiao_ble-zmk.uf2`

> [!NOTE]
> `settings_reset`用ファームウェアは、分割キーボードのペアリングやBluetooth接続に関する設定を初期化するために書き込むクリーンアップ用のファームウェアです。


## ブートローダーモードへの入り方

XIAO nRF52840 Plusをブートローダーモードにすると、`XIAO-SENSE`という名前のUSBドライブとして認識されます。

1. PCと書き込み対象のXIAO nRF52840 PlusをUSB Type-Cケーブルで接続します。
2. RESETボタンを爪楊枝の背中などを使い素早く2回押します。（ダブルクリック）

    todo：リセットボタンの画像

3. `XIAO-SENSE`ドライブが表示されます。

## ファームウェアの書き込み

UF2ファイルは、`XIAO-SENSE`ドライブへドラッグ&ドロップ、またはFinder / エクスプローラーからコピーして書き込みます。

1. 左側のXIAO nRF52840 Plusをブートローダーモードにします。
2. `XIAO-SENSE`ドライブに`settings_reset-seeeduino_xiao_ble-zmk.uf2`を書き込みます。
3. 書き込みが完了すると`XIAO-SENSE`ドライブが消え、自動的に再起動します。
4. もう一度ブートローダーモードにします。
5. `XIAO-SENSE`ドライブに`cleave_hhjp_left.rgbled_adapter-seeeduino_xiao_ble-zmk.uf2`を書き込みます。
6. 右側も同様に、`settings_reset-seeeduino_xiao_ble-zmk.uf2`と`cleave_hhjp_right.rgbled_adapter-seeeduino_xiao_ble-zmk.uf2`を順番に書き込みます。
7. 両方の書き込みが完了したら左右の電源を入れます。
8. PCのBluetooth設定で`Cleave HHJP`が認識され、接続できれば書き込みは完了です。
> [!NOTE]
> macOSではUF2ファイルのコピー後にエラーが表示される場合がありますが、コピー後に`XIAO-SENSE`ドライブが消えて自動的に再起動していれば、書き込みは完了しています。

## 接続確認・動作確認

Bluetooth接続が完了したら、すべてのキーが認識されるか確認してください。

左右間のペアリングは電源を入れると自動的に行われます。有線接続でも動作確認はできますが、左右間は無線で接続されます。

### 左右のペアリングについて

ZMKの分割キーボードでは、左右は**シールド名から生成されるBluetooth UUID**を使って自動的にペアリングされます。

Bluetoothの接続範囲内にすでに動作しているCleave HHJPが存在する場合は、初回起動時に意図しない組み合わせでペアリングされる可能性があります。

#### 安全な運用方法

- **初回セットアップ時**: 1ペアずつ電源を入れてペアリングを確立する
- **ペアリングリセット後**: 他のペアの電源を切った状態で再ペアリングする

一度正しくペアリングが確立すれば、MACアドレスで記憶されるため、以降は複数ペアを同時に使用しても問題ありません。

## トラブルシューティング

### ブートローダーモードに入らない

### UF2 コピー後にドライブが消えない

### Bluetooth に表示されない

### 左右が接続しない

### 一部のキーが反応しない

[次: Cleave HHJP ケースビルドガイド](03-CASE.md)

[目次に戻る](README.md)
