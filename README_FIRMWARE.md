# Cleave HHJP - ZMK Firmware

このディレクトリには、Cleave HHJP（HHKB JP分割Bluetoothキーボード）用のZMKファームウェア設定が含まれています。

## 概要

- **キーボード名**: Cleave HHJP (HHKB JP Split Bluetooth Keyboard)
- **キー配列**: HHKB JP配列ベース
- **総キー数**: 70キー（左31 + 右39）
- **マイコン**: Seeed XIAO nRF52840 Plus × 2個
- **接続方式**: Bluetooth 5.0 BLE（完全ワイヤレス）
- **ファームウェア**: ZMK (Zephyr Mechanical Keyboard)

## ビルド方法

### GitHub Actionsによる自動ビルド（推奨）

このリポジトリは、GitHub Actionsによる自動ビルドが設定されています。

1. `firmware/`ディレクトリ配下のファイルを変更
2. GitHubにプッシュ
3. GitHub Actionsが自動的にビルドを実行
4. ビルド完了後、Actionsページから`.uf2`ファイルをダウンロード

### ローカルビルド

Dockerを使用したローカルビルドも可能です。

```bash
# ファームウェアディレクトリに移動
cd firmware

# West初期化
docker run --rm -v $(pwd):/workspace -w /workspace zmkfirmware/zmk-build-arm:stable \
  sh -c "west init -l config && west update && west zephyr-export"

# 左側ビルド
docker run --rm -v $(pwd):/workspace -w /workspace zmkfirmware/zmk-build-arm:stable \
  west build -s zmk/app -b seeeduino_xiao_ble -- -DSHIELD=cleave_hhjp_left -DZMK_CONFIG=/workspace/config

# UF2ファイルをコピー
cp build/zephyr/zmk.uf2 cleave_hhjp_left-seeeduino_xiao_ble-zmk.uf2

# ビルドディレクトリをクリーンアップ
rm -rf build

# 右側ビルド
docker run --rm -v $(pwd):/workspace -w /workspace zmkfirmware/zmk-build-arm:stable \
  west build -s zmk/app -b seeeduino_xiao_ble -- -DSHIELD=cleave_hhjp_right -DZMK_CONFIG=/workspace/config

# UF2ファイルをコピー
cp build/zephyr/zmk.uf2 cleave_hhjp_right-seeeduino_xiao_ble-zmk.uf2
```

## ファームウェア書き込み

### 必要なもの

- 組み立て済みのキーボード（左右両側）
- USB Type-Cケーブル
- ビルド済みUF2ファイル（左右各1つ）

### 書き込み手順

#### 左側

1. 左側キーボードの電源スイッチをONにする
2. USB Type-CケーブルでPCに接続
3. XIAOのリセットボタンを**ダブルクリック**
4. オレンジ色のLEDがゆっくり点滅（ブートローダーモード）
5. 「XIAO-SENSE」または「XIAO-nRF52」というドライブがマウントされる
6. `cleave_hhjp_left-seeeduino_xiao_ble-zmk.uf2` をドライブにコピー
7. 自動的に書き込みが開始され、完了後に自動でアンマウント
8. 左側キーボードが再起動

#### 右側

左側と同様の手順を右側キーボードでも実行：

1. 右側キーボードの電源スイッチをONにする
2. USB Type-CケーブルでPCに接続
3. XIAOのリセットボタンを**ダブルクリック**
4. `cleave_hhjp_right-seeeduino_xiao_ble-zmk.uf2` をコピー
5. 自動的に再起動

## ペアリング（完全ワイヤレス動作）

### 左右接続（自動）

1. 左右両側の電源をONにする
2. 左側（セントラル）が右側（ペリフェラル）を自動検出・接続
3. 接続完了後、User LEDが青色点滅（接続済み）

### ホストPC接続（Bluetooth）

1. **USB接続を外す**
2. PCのBluetooth設定を開く
3. 「Cleave HHJP」という名前のデバイスが表示される
4. ペアリング実行
5. 接続完了後、キーボードが**完全ワイヤレス**で使用可能

### 複数デバイス切替

- Bluetoothレイヤー（Fn + Alt）で最大5台のデバイスを切替可能
- `BT_SEL 0`〜`BT_SEL 4` でデバイス切替
- 例: PC、Mac、タブレット、スマートフォン等を切り替えて使用可能

## キーマップ

### レイヤー構成

| レイヤー | 名前 | 用途 |
|---------|------|------|
| 0 | Default | デフォルトキー配列（HHKB JP配列） |
| 1 | Fn | Fnキー押下時（F1-F12、メディア、ナビゲーション） |
| 2 | BT | Bluetooth制御（デバイス切替、ペアリング） |

### ZMK Studioでのキーマップ変更

ZMK Studioを使用すると、GUIでキーマップを動的に変更できます。

1. ブラウザで https://zmk.studio を開く
2. **左側キーボードをUSB接続**
3. 「Connect」ボタンでキーボードを検出
4. GUIでキーマップを編集・保存
5. USB接続を外しても設定が保持される

**注意**: ZMK StudioはUSB接続時のみ使用可能です。設定変更後は、完全ワイヤレスで動作します。

## 主要機能

- **ZMK Studio対応**: 動的キーマップ変更
- **Bluetooth 5デバイス切替**: 最大5台のデバイスをペアリング
- **バッテリー残量表示**: 内蔵RGB LED
- **完全ワイヤレス動作**: 左右BLE接続 + ホストBLE接続
- **スリープ/ウェイク機能**: 15分でスリープ移行
- **USB充電**: 電源スイッチOFF時も充電可能

## LED表示パターン

| 状態 | 色 | パターン |
|------|-----|----------|
| 高バッテリー残量 (>80%) | 緑 | 点滅 |
| 中バッテリー残量 (20-80%) | 黄 | 点滅 |
| BLE接続済み | 青 | 点滅 |
| 低バッテリー残量 (<20%) | 赤 | 点滅 |
| 未接続 | 赤 | 点滅 |

## ファイル構成

```
firmware/
├── config/
│   ├── boards/shields/cleave_hhjp/
│   │   ├── Kconfig.shield              # シールド宣言
│   │   ├── Kconfig.defconfig           # デフォルト設定
│   │   ├── cleave_hhjp.conf            # 共通設定
│   │   ├── cleave_hhjp.dtsi            # 共通デバイスツリー
│   │   ├── cleave_hhjp.keymap          # キーマップ定義
│   │   ├── cleave_hhjp_left.overlay    # 左側ハードウェア定義
│   │   ├── cleave_hhjp_left.conf       # 左側設定
│   │   ├── cleave_hhjp_right.overlay   # 右側ハードウェア定義
│   │   └── cleave_hhjp_right.conf      # 右側設定
│   └── west.yml                        # 依存関係定義
├── build.yaml                          # ビルドターゲット定義
└── README.md                           # このファイル
```

## 参考リンク

- [ZMK公式ドキュメント](https://zmk.dev/)
- [ZMK Studio](https://zmk.studio)
- [Seeed XIAO nRF52840](https://wiki.seeedstudio.com/XIAO_BLE/)
- [zmk-rgbled-widget](https://github.com/caksoylar/zmk-rgbled-widget)
