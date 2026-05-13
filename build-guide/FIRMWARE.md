

## 左右のペアリングについて

ZMKの分割キーボードでは、左右は**シールド名から生成されるBluetooth UUID**を使って自動的にペアリングされます。

Bluetoothの接続範囲内にすでに動作しているCleave HHJPが存在する場合は初回起動時に意図しない組み合わせでペアリングされる可能性があります。

#### 安全な運用方法

- **初回セットアップ時**: 1ペアずつ電源を入れてペアリングを確立する
- **ペアリングリセット後**: 他のペアの電源を切った状態で再ペアリングする

一度正しくペアリングが確立すれば、MACアドレスで記憶されるため、以降は複数ペアを同時に使用しても問題ありません。

## ファームウェアの書き込み（macOS）

XIAO BLE nRF52840をブートローダーモードにすると、`XIAO-SENSE`という名前のUSBドライブとしてマウントされます。

### ブートローダーモードへの入り方

1. RESETボタンを素早く2回押す（ダブルクリック）
2. 緑色のLEDがゆっくり点滅し、`XIAO-SENSE`ドライブがマウントされる

### 書き込みコマンド

```bash
# 左側
cp cleave_hhjp_left-seeeduino_xiao_ble.uf2 /Volumes/XIAO-SENSE/

# 右側
cp cleave_hhjp_right-seeeduino_xiao_ble.uf2 /Volumes/XIAO-SENSE/
```

コピーが完了すると自動的に再起動し、ファームウェアが適用されます。

## ファームウェアの書き込み（Windows）

XIAO BLE nRF52840をブートローダーモードにすると、`XIAO-SENSE`という名前のUSBドライブとして認識されます。

### ブートローダーモードへの入り方

1. RESETボタンを素早く2回押す（ダブルクリック）
2. 緑色のLEDがゆっくり点滅し、`XIAO-SENSE`ドライブがエクスプローラーに表示される

### 書き込みコマンド（PowerShell）

```powershell
# 左側（ドライブレターは環境に応じて変更）
Copy-Item cleave_hhjp_left-seeeduino_xiao_ble.uf2 D:\

# 右側
Copy-Item cleave_hhjp_right-seeeduino_xiao_ble.uf2 D:\
```

### 書き込みコマンド（コマンドプロンプト）

```cmd
REM 左側（ドライブレターは環境に応じて変更）
copy cleave_hhjp_left-seeeduino_xiao_ble.uf2 D:\

REM 右側
copy cleave_hhjp_right-seeeduino_xiao_ble.uf2 D:\
```

エクスプローラーから直接uf2ファイルをドラッグ&ドロップしても書き込めます。コピーが完了すると自動的に再起動し、ファームウェアが適用されます。
