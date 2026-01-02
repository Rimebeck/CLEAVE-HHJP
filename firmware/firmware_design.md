# ZMKファームウェア設計書
## HHKB JP分割Bluetoothキーボード (Cleave HHJP)

---

## 1. プロジェクト概要

### 1.1 キーボード仕様

| 項目 | 仕様 |
|------|------|
| 製品名 | Cleave HHJP (HHKB JP分割Bluetoothキーボード) |
| キー配列 | HHKB JP配列ベース |
| 総キー数 | 70キー（左31 + 右39） |
| 分割方式 | 左右分割・無線接続 |
| 接続方式 | Bluetooth 5.0 BLE |
| ホットスワップ | Kailh MX/Chocソケット対応 |

### 1.2 技術スタック

| 要素 | 採用技術 |
|------|----------|
| ファームウェア | ZMK (Zephyr Mechanical Keyboard) |
| ビルドシステム | GitHub Actions |
| キーマップ編集 | ZMK Studio対応（GUIによる動的変更可能） |
| 開発環境 | West (Zephyr Build System) |
| 配布形式 | UF2ファイル（USB Mass Storageで書き込み） |

### 1.3 主要機能

- ZMK Studio対応による動的キーマップ変更
- Bluetooth 5デバイス切替
- バッテリー残量表示（内蔵RGB LED）
- 無線分割通信（左右BLE接続）
- スリープ/ウェイク機能
- USB充電（電源スイッチOFF時も充電可能）

---

## 2. ハードウェア仕様

### 2.1 マイコン (MCU)

| 項目 | 仕様 |
|------|------|
| モデル | Seeed XIAO nRF52840 Plus |
| プロセッサ | ARM Cortex-M4 64MHz (Nordic nRF52840) |
| メモリ | 256KB RAM / 1MB Flash |
| Bluetooth | Bluetooth 5.0 / BLE |
| GPIO数 | 20ピン（表面11 + 端面スルーホール9） |
| 技適認証 | 222-257139 |
| 動作電圧 | 3.3V |
| 実装方法 | 直接SMDはんだ付け（ピンヘッダ不使用） |
| 数量 | 2個（左右各1個） |

**採用理由**:
- GPIO 20本（通常版13本）で左右両側の大型マトリックスに対応
- 追加ピンは端面スルーホール（1.27mmピッチ）で裏面パッドへの細線はんだ付け不要
- 内蔵LiPo充電回路（BQ25101）とRGB LED搭載
- 技適認証済みでBluetoothキーボードの販売・頒布が可能

### 2.2 キーマトリックス構成

#### 左側PCB

| 項目 | 仕様 |
|------|------|
| マトリックス | 7列（COL0-6） × 5行（ROW0-4） |
| キー数 | 31キー |
| スキャン方式 | COL2ROW |
| ダイオード | 1N4148W (SOD-123) × 31個 |

**キー配置**:
```
Row 0: Esc, 1, 2, 3, 4, 5, 6
Row 1: Tab, Q, W, E, R, T
Row 2: Ctrl, A, S, D, F, G
Row 3: Shift, Z, X, C, V, B
Row 4: Fn, Win, Cmd, Alt, 英/日, Space (左側)
```

#### 右側PCB

| 項目 | 仕様 |
|------|------|
| マトリックス | 8列（COL0-7） × 5行（ROW0-4） |
| キー数 | 39キー |
| スキャン方式 | COL2ROW |
| ダイオード | 1N4148W (SOD-123) × 39個 |

**キー配置**:
```
Row 0: 7, 8, 9, 0, -, ^, ¥, Backspace
Row 1: Y, U, I, O, P, @, [, Enter
Row 2: H, J, K, L, ;, :, ]
Row 3: N, M, <, >, /, _, ↑, Shift
Row 4: Space (右側), 英/日, カナ, Alt, Fn, ←, ↓, →
```

### 2.3 ピンアサイン

#### 左側PCB - ピン配置

| 機能 | 論理名 | 物理ピン | nRF52840ピン | 備考 |
|------|--------|----------|--------------|------|
| COL0 | D0 | P0.02 | P0.02 | 表面パッド |
| COL1 | D1 | P0.03 | P0.03 | 表面パッド |
| COL2 | D2 | P0.28 | P0.28 | 表面パッド |
| COL3 | D3 | P0.29 | P0.29 | 表面パッド |
| COL4 | D4 | P0.04 | P0.04 | 表面パッド |
| COL5 | D5 | P0.05 | P0.05 | 表面パッド |
| COL6 | D6 | P1.11 | P1.11 | 表面パッド |
| ROW0 | D7 | P1.12 | P1.12 | 表面パッド |
| ROW1 | D8 | P1.13 | P1.13 | 表面パッド |
| ROW2 | D9 | P1.14 | P1.14 | 表面パッド |
| ROW3 | D10 | P1.15 | P1.15 | 表面パッド |
| ROW4 | D11 | P0.09 | P0.09 | **端面TH** / NFC1 |
| 予備 | D12 | P0.10 | P0.10 | **端面TH** / NFC2 |

**重要**: D11 (P0.09) はNFCピンのため、`CONFIG_NFCT_PINS_AS_GPIOS=y` の設定が**必須**です。

#### 右側PCB - ピン配置

| 機能 | 論理名 | 物理ピン | nRF52840ピン | 備考 |
|------|--------|----------|--------------|------|
| COL0 | D0 | P0.02 | P0.02 | 表面パッド |
| COL1 | D1 | P0.03 | P0.03 | 表面パッド |
| COL2 | D2 | P0.28 | P0.28 | 表面パッド |
| COL3 | D3 | P0.29 | P0.29 | 表面パッド |
| COL4 | D4 | P0.04 | P0.04 | 表面パッド |
| COL5 | D5 | P0.05 | P0.05 | 表面パッド |
| COL6 | D6 | P1.11 | P1.11 | 表面パッド |
| COL7 | D7 | P1.12 | P1.12 | 表面パッド |
| ROW0 | D8 | P1.13 | P1.13 | 表面パッド |
| ROW1 | D9 | P1.14 | P1.14 | 表面パッド |
| ROW2 | D10 | P1.15 | P1.15 | 表面パッド |
| ROW3 | D11 | P0.09 | P0.09 | **端面TH** / NFC1 |
| ROW4 | D12 | P0.10 | P0.10 | **端面TH** / NFC2 |
| 予備 | D13 | P0.13 | P0.13 | **端面TH** |

**重要**: D11 (P0.09) とD12 (P0.10) はNFCピンのため、`CONFIG_NFCT_PINS_AS_GPIOS=y` の設定が**必須**です。

### 2.4 LED仕様

#### ステータス表示LED

| 項目 | 仕様 |
|------|------|
| LED種別 | XIAO nRF52840 Plus内蔵User LED（RGB 3-in-1） |
| 赤色ピン | P0.26 (GPIO_ACTIVE_LOW) |
| 緑色ピン | P0.30 (GPIO_ACTIVE_LOW) |
| 青色ピン | P0.06 (GPIO_ACTIVE_LOW) |
| 制御方法 | zmk-rgbled-widget（外部モジュール） |
| 外部部品 | 不要（MCU内蔵） |

**LED表示パターン** (zmk-rgbled-widget):

| 状態 | 色 | パターン | 表示タイミング |
|------|-----|----------|----------------|
| 高バッテリー残量 (>80%) | 緑 | 点滅 | 起動時のみ |
| 中バッテリー残量 (20-80%) | 黄 | 点滅 | アドバタイジング中 |
| BLE接続済み | 青 | 点滅 | 接続確立時 |
| 低バッテリー残量 (<20%) | 赤 | 点滅 | 警告表示 |
| 未接続 | 赤 | 点滅 | 切断時 |

#### 充電LED

| 項目 | 仕様 |
|------|------|
| LED種別 | XIAO nRF52840 Plus内蔵充電LED |
| 制御 | 自動（BQ25101 ICによる） |
| 赤色 | 充電中 |
| 緑色 | 充電完了 |

**注**: 充電LEDはUser LEDとは別系統で、ファームウェアからの制御は不要です。

### 2.5 電源仕様

#### バッテリー

| 項目 | 仕様 |
|------|------|
| 種別 | LiPo (リチウムポリマー) |
| 容量選択肢 | 100mAh または 250mAh |
| 公称電圧 | 3.7V |
| 動作範囲 | 3.2V - 4.2V |
| コネクタ | JST PH 2ピン |
| 数量 | 2個（左右各1個、独立動作） |

**推定動作時間**:
- 100mAh: アクティブ約1週間 / ディープスリープ約2ヶ月
- 250mAh: アクティブ約2.5週間 / ディープスリープ約5ヶ月

#### 充電回路

| 項目 | 仕様 |
|------|------|
| 充電IC | BQ25101 (XIAO内蔵) |
| 充電電流 | 最大50mA |
| 充電方式 | USB Type-C経由 |
| 充電制御 | 自動（CC/CV方式） |

#### 電源スイッチ・バイパス回路

| 項目 | 仕様 |
|------|------|
| スイッチ | MSK-12C02 スライドスイッチ（左右各1個） |
| バイパスダイオード | B5819W ショットキーダイオード (SOD-123) |
| 特殊機能 | 電源スイッチOFF時もUSB充電可能 |

**回路構成**:
```
XIAO BAT+ --+-- [MSK-12C02 スイッチ] --+-- LiPo BAT+
            |                          |
            +-- [B5819W ショットキー] --+
                 (カソードをバッテリー側へ)

XIAO GND -------- LiPo BAT-
```

**動作**:
- スイッチON: 通常動作、バッテリーから直接給電
- スイッチOFF: キーボード動作停止、ダイオード経由でUSB充電のみ可能

### 2.6 分割通信仕様

| 項目 | 仕様 |
|------|------|
| 通信方式 | **完全無線** - Bluetooth 5.0 BLE（左右分割 + ホスト接続） |
| 左側役割 | **セントラル＆ペリフェラル**（右側XIAOへ接続 + ホストPCから接続を受付） |
| 右側役割 | ペリフェラル（左側XIAOにBLE接続） |
| ホスト接続 | **Bluetooth 5.0 BLE**（常時利用は完全無線） |
| USB接続用途 | ファームウェア更新・充電・ZMK Studio接続のみ |
| 通信プロトコル | ZMK Split BLE |

**通信フロー**:
```
[ホストPC/Mac] <-- BLE --> [左側XIAO] <-- BLE --> [右側XIAO]
                           (Central &              (Peripheral)
                            Peripheral)
```

**重要な設計ポイント**:
- **左側XIAOは二重役割**: 右側XIAOに対してはセントラル、ホストPCに対してはペリフェラル
- **常時利用は完全無線**: USB接続はファームウェア更新、充電、デバッグ時のみ使用
- **遅延特性**: 右側キーは左側経由でホストに送信されるため、10-20ms程度の遅延が発生
- **USB接続時の動作**: USB接続中もBluetoothホスト接続が優先され、USBはデバッグログ出力のみに使用

---

## 3. ZMKファームウェアアーキテクチャ

### 3.1 ZMK概要

ZMKは、Zephyr RTOSをベースとした次世代キーボードファームウェアです。以下の特徴があります：

- **無線ファースト設計**: Bluetooth LEを標準サポート
- **低消費電力**: スリープ/アイドル機能による長時間動作
- **Zephyr RTOS**: 堅牢なリアルタイムOS基盤
- **デバイスツリー記述**: ハードウェア構成を宣言的に定義
- **GitHub Actions統合**: プッシュ時に自動ビルド

### 3.2 プロジェクト構造

ZMK用のファームウェアは、**zmk-configリポジトリ**として構築します。以下の構造を採用します：

```
zmk-config/
├── .github/
│   └── workflows/
│       └── build.yml              # GitHub Actionsビルド定義
├── config/
│   ├── boards/
│   │   └── shields/
│   │       └── cleave_hhjp/
│   │           ├── cleave_hhjp.conf            # 共通設定
│   │           ├── cleave_hhjp.dtsi            # 共通デバイスツリー
│   │           ├── cleave_hhjp.keymap          # キーマップ定義
│   │           ├── cleave_hhjp_left.overlay    # 左側ハードウェア定義
│   │           ├── cleave_hhjp_left.conf       # 左側設定
│   │           ├── cleave_hhjp_right.overlay   # 右側ハードウェア定義
│   │           ├── cleave_hhjp_right.conf      # 右側設定
│   │           ├── Kconfig.shield                # シールド宣言
│   │           └── Kconfig.defconfig             # デフォルト設定
│   └── west.yml                   # Westマニフェスト（依存関係定義）
├── build.yaml                     # ビルドターゲット定義
└── README.md                      # プロジェクト説明
```

### 3.3 必須ファイル一覧

#### 3.3.1 GitHub Actions設定

| ファイル | 役割 |
|---------|------|
| `.github/workflows/build.yml` | プッシュ時の自動ビルド、UF2生成、Release配置 |

#### 3.3.2 シールド定義ファイル

| ファイル | 役割 |
|---------|------|
| `config/boards/shields/cleave_hhjp/Kconfig.shield` | シールド名とボード互換性の宣言 |
| `config/boards/shields/cleave_hhjp/Kconfig.defconfig` | シールド選択時のデフォルト設定 |

#### 3.3.3 ハードウェア定義ファイル (デバイスツリー)

| ファイル | 役割 |
|---------|------|
| `cleave_hhjp.dtsi` | 左右共通のデバイスツリー定義（LEDなど） |
| `cleave_hhjp_left.overlay` | 左側キーマトリックス定義（7列×5行） |
| `cleave_hhjp_right.overlay` | 右側キーマトリックス定義（8列×5行） |

#### 3.3.4 設定ファイル (Kconfig)

| ファイル | 役割 |
|---------|------|
| `cleave_hhjp.conf` | 左右共通設定（NFC GPIO化、分割BLE、スリープ等） |
| `cleave_hhjp_left.conf` | 左側専用設定（セントラル役割） |
| `cleave_hhjp_right.conf` | 右側専用設定（ペリフェラル役割） |

#### 3.3.5 キーマップファイル

| ファイル | 役割 |
|---------|------|
| `cleave_hhjp.keymap` | キー配列定義、レイヤー定義、ZMK Studio設定 |

#### 3.3.6 依存関係・ビルド設定

| ファイル | 役割 |
|---------|------|
| `config/west.yml` | Westマニフェスト（zmk-rgbled-widget等の外部依存） |
| `build.yaml` | ビルドターゲット定義（左右両側を自動ビルド） |

### 3.4 外部依存関係

このキーボードは、以下の外部ZMKモジュールに依存します：

| モジュール | リポジトリURL | 役割 |
|-----------|---------------|------|
| zmk-rgbled-widget | `https://github.com/caksoylar/zmk-rgbled-widget` | 内蔵RGB LEDによるステータス表示 |

**west.yml への追加方法**:
- `manifest.remotes` にリポジトリ情報を追加
- `manifest.projects` に依存モジュールを追加
- zmk-configテンプレートから初期化時に自動で含まれる構造を利用

---

## 4. 必須ZMK設定項目

### 4.1 共通設定 (cleave_hhjp.conf)

以下の設定を `cleave_hhjp.conf` に記述します：

| 設定項目 | 値 | 説明 |
|---------|-----|------|
| `CONFIG_NFCT_PINS_AS_GPIOS` | `y` | **必須**: D11/D12（NFC1/NFC2）をGPIOとして使用 |
| `CONFIG_ZMK_SPLIT` | `y` | 分割キーボード有効化 |
| `CONFIG_ZMK_SPLIT_BLE` | `y` | Bluetooth分割通信有効化 |
| `CONFIG_BT_CTLR_TX_PWR_PLUS_8` | `y` | BLE送信出力を+8dBmに増強（分割通信の安定化） |
| `CONFIG_ZMK_BATTERY_REPORTING` | `y` | バッテリー残量報告有効化 |
| `CONFIG_ZMK_SPLIT_BLE_CENTRAL_BATTERY_LEVEL_FETCHING` | `y` | セントラル側がペリフェラル側のバッテリー残量取得 |
| `CONFIG_ZMK_SPLIT_BLE_CENTRAL_BATTERY_LEVEL_PROXY` | `y` | ペリフェラル側バッテリー残量をホストに転送 |
| `CONFIG_ZMK_IDLE_TIMEOUT` | `30000` | アイドルタイムアウト 30秒 (30000ms) |
| `CONFIG_ZMK_SLEEP` | `y` | スリープモード有効化 |
| `CONFIG_ZMK_IDLE_SLEEP_TIMEOUT` | `900000` | スリープ移行時間 15分 (900000ms) |
| `CONFIG_ZMK_STUDIO` | `y` | **ZMK Studio有効化（動的キーマップ変更）** |

**重要**: `CONFIG_NFCT_PINS_AS_GPIOS=y` がない場合、D11/D12がGPIOとして動作せず、マトリックススキャンが正常に動作しません。

### 4.2 左側設定 (cleave_hhjp_left.conf)

| 設定項目 | 値 | 説明 |
|---------|-----|------|
| `CONFIG_ZMK_SPLIT_ROLE_CENTRAL` | `y` | セントラル役割（右側XIAOへの接続） |
| `CONFIG_ZMK_SPLIT_BLE_CENTRAL_PERIPHERALS` | `1` | セントラルが接続するペリフェラル数（右側の1台） |

**注**: 左側はセントラルとして右側に接続しつつ、ホストPCに対してはペリフェラルとして動作します。ZMKではこの構成が標準でサポートされており、追加設定は不要です。

### 4.3 右側設定 (cleave_hhjp_right.conf)

| 設定項目 | 値 | 説明 |
|---------|-----|------|
| `CONFIG_ZMK_SPLIT_BLE_ROLE_PERIPHERAL` | `y` | ペリフェラル役割（左側に接続） |

### 4.4 完全ワイヤレス動作のための重要設定

**完全ワイヤレス動作（USB不使用）を実現するため、以下の設定が重要です**:

| 設定項目 | 値 | 説明 |
|---------|-----|------|
| `CONFIG_ZMK_BLE_EXPERIMENTAL_CONN` | `y` | **推奨**: BLE接続の実験的改善（接続安定性向上） |
| `CONFIG_BT_MAX_CONN` | `6` | 最大BLE接続数（右側分割1 + ホスト5デバイス） |
| `CONFIG_BT_MAX_PAIRED` | `6` | 最大ペアリングデバイス数 |

**注**: ZMKは標準で、USB接続時はUSBを優先、USB未接続時は自動的にBluetooth動作に切り替わります。完全ワイヤレス動作のために追加の設定は不要です。

### 4.5 その他推奨設定（オプション）

以下は必要に応じて `cleave_hhjp.conf` に追加可能な設定です：

| 設定項目 | 値 | 説明 |
|---------|-----|------|
| `CONFIG_ZMK_USB_LOGGING` | `y` | USB経由でのデバッグログ出力（開発時のみ、常時利用では不要） |
| `CONFIG_BT_GATT_ENFORCE_SUBSCRIPTION` | `n` | GATT購読強制を無効化（一部ホストとの互換性向上） |

---

## 5. デバイスツリー構造

### 5.1 デバイスツリー概要

ZMKでは、ハードウェア構成を**デバイスツリー**（Device Tree）形式で記述します。これはLinuxカーネルやZephyr RTOSで使用される標準的なハードウェア記述方法です。

### 5.2 共通デバイスツリー (cleave_hhjp.dtsi)

左右共通のハードウェア定義を記述します：

**記述内容**:
- zmk-rgbled-widget によるRGB LED制御設定
- User LED (Red: P0.26, Green: P0.30, Blue: P0.06) の定義
- LEDステータス表示のノード定義

**技術的ポイント**:
- `compatible = "zmk,rgbled-widget"`
- GPIOピンは`GPIO_ACTIVE_LOW`指定（XIAO仕様）
- デバイスツリーの`chosen`ノードでグローバル参照可能に設定

### 5.3 左側オーバーレイ (cleave_hhjp_left.overlay)

**記述内容**:
- キーマトリックス: 7列 × 5行
- 列ピン (COL0-6): D0-D6 (P0.02, P0.03, P0.28, P0.29, P0.04, P0.05, P1.11)
- 行ピン (ROW0-4): D7-D11 (P1.12, P1.13, P1.14, P1.15, P0.09)
- ダイオード方向: `col2row`
- キー配置: HHKB JP左側31キーの物理配置

**技術的ポイント**:
- `compatible = "zmk,matrix-transform"`
- 行・列ピンは`<&gpio0 X GPIO_ACTIVE_HIGH>`形式で指定
- 行ピンに`(GPIO_ACTIVE_HIGH | GPIO_PULL_DOWN)`でプルダウン設定
- rc-to-xy座標マッピングで物理配置を定義

### 5.4 右側オーバーレイ (cleave_hhjp_right.overlay)

**記述内容**:
- キーマトリックス: 8列 × 5行
- 列ピン (COL0-7): D0-D7 (P0.02, P0.03, P0.28, P0.29, P0.04, P0.05, P1.11, P1.12)
- 行ピン (ROW0-4): D8-D12 (P1.13, P1.14, P1.15, P0.09, P0.10)
- ダイオード方向: `col2row`
- キー配置: HHKB JP右側39キーの物理配置

**技術的ポイント**:
- 左側と同様の構造
- D11 (P0.09) とD12 (P0.10) はNFCピンのため、`CONFIG_NFCT_PINS_AS_GPIOS=y` が必須
- 右側は列が8列に増加（左側は7列）

---

## 6. キーマップ定義

### 6.1 キーマップファイル構造

`cleave_hhjp.keymap` ファイルには以下を記述します：

1. **physical_layout** - ZMK Studio用の物理レイアウト定義（70キーの座標）
2. **keymap** - 実際のキー割り当て
3. **レイヤー定義** - 複数レイヤー（デフォルト、Fn、Bluetooth制御等）

### 6.2 ZMK Studio対応 - physical_layout

ZMK Studioで動的にキーマップを変更可能にするため、`physical_layout` を定義します：

**記述内容**:
- 70キーすべての物理座標 (x, y) をミリメートル単位で定義
- キーサイズ (width, height) を定義
- キーの回転角度 (rotation) を定義（通常は0）
- transform参照を設定

**技術的ポイント**:
- `compatible = "zmk,physical-layout"`
- `display-name` でレイアウト名を指定
- `keys` プロパティに全キーの座標配列を記述
- ZMK Studioアプリで視覚的にキーマップを編集可能

### 6.3 レイヤー構成

| レイヤー番号 | レイヤー名 | 用途 |
|-------------|-----------|------|
| 0 | default_layer | デフォルトキー配列（HHKB JP配列） |
| 1 | fn_layer | Fnキー押下時（F1-F12、メディア、ナビゲーション） |
| 2 | bt_layer | Bluetooth制御（デバイス切替、ペアリング） |

**デフォルトレイヤー (Layer 0)**:
- HHKB JP配列の標準キー配置
- 左右70キーすべてのキーコードを定義

**Fnレイヤー (Layer 1)**:
- ファンクションキー (F1-F12)
- メディアキー (再生/一時停止、音量調整等)
- 画面輝度調整
- 矢印キー等のナビゲーション

**Bluetoothレイヤー (Layer 2)**:
- デバイス切替 (BT0-BT4): 最大5台までペアリング可能
- ペアリングクリア
- Bluetoothプロファイル管理

### 6.4 キーコード例

以下はZMKで使用する主なキーコード例です：

| 機能 | ZMKキーコード |
|------|---------------|
| 通常キー | `&kp A`, `&kp N1`, `&kp ESC` 等 |
| レイヤー切替 | `&mo 1` (押下中のみ), `&to 2` (トグル) |
| Bluetooth | `&bt BT_SEL 0`, `&bt BT_CLR` |
| メディア | `&kp C_PP` (再生/一時停止), `&kp C_VOL_UP` |
| 修飾キー | `&kp LSHIFT`, `&kp LCTRL`, `&kp LALT` |

---

## 7. ZMK Studio完全対応

### 7.1 ZMK Studioとは

ZMK Studioは、ZMKファームウェアのキーマップをGUIで動的に変更できるツールです。以下の特徴があります：

- **リアルタイム変更**: キーボードをPCに接続したまま、キーマップを即座に変更
- **ビジュアル編集**: 物理レイアウトを視覚的に表示し、ドラッグ&ドロップでキー割り当て
- **ファームウェア書き込み不要**: 変更はキーボード内のフラッシュメモリに保存
- **Webアプリ**: ブラウザから https://zmk.studio でアクセス可能

### 7.2 ZMK Studio有効化手順

**1. ファームウェア設定**

`cleave_hhjp.conf` に以下を追加：

```
CONFIG_ZMK_STUDIO=y
```

**2. physical_layout定義**

`cleave_hhjp.keymap` に70キー分の物理座標を定義（6.2節参照）

**3. ビルド・書き込み**

GitHub ActionsでビルドされたUF2ファイルをXIAOに書き込み

**4. ZMK Studioアクセス**

- ブラウザで https://zmk.studio を開く
- **左側キーボードをUSB接続**（ZMK StudioはUSB経由でのみ接続可能）
- 「Connect」ボタンでキーボードを検出
- GUIでキーマップを編集・保存

**重要**: ZMK Studioでの設定変更はUSB接続が必要ですが、設定完了後の常時利用は完全ワイヤレスで動作します。

### 7.3 ZMK Studioで可能な操作

| 操作 | 説明 |
|------|------|
| キー割り当て変更 | 任意のキーに別のキーコードを割り当て |
| レイヤー追加/削除 | 新しいレイヤーを作成、既存レイヤーを削除 |
| Bluetooth設定 | デバイス切替キーの配置変更 |
| マクロ作成 | 複数キー入力のマクロを定義 |
| 設定エクスポート | 現在のキーマップをJSON形式でエクスポート |

---

## 8. ビルドシステム（GitHub Actions）

### 8.1 GitHub Actions概要

このプロジェクトでは、GitHub Actionsを使用して以下を自動化します：

1. zmk-configリポジトリへのコミット/プッシュを検知
2. ZMKファームウェアを左右両側分ビルド
3. UF2ファイルを生成
4. GitHubリリースに自動アップロード

### 8.2 ビルドワークフロー (.github/workflows/build.yml)

**トリガー**:
- `main` ブランチへのプッシュ
- `config/` ディレクトリの変更

**ビルドターゲット** (`build.yaml` で定義):
- `cleave_hhjp_left` (左側)
- `cleave_hhjp_right` (右側)

**成果物**:
- `cleave_hhjp_left-seeeduino_xiao_ble-zmk.uf2`
- `cleave_hhjp_right-seeeduino_xiao_ble-zmk.uf2`

### 8.3 ビルドプロセス

1. **環境構築**: Zephyr SDK、West、依存パッケージのインストール
2. **Westワークスペース初期化**: `west init`, `west update`
3. **ビルド実行**: `west build` で左右両側をビルド
4. **アーティファクトアップロード**: UF2ファイルをGitHub Artifactsに保存
5. **リリース作成**: タグプッシュ時に自動でRelease作成、UF2添付

### 8.4 build.yaml 定義

**記述内容**:
```yaml
include:
  - board: seeeduino_xiao_ble
    shield: cleave_hhjp_left
  - board: seeeduino_xiao_ble
    shield: cleave_hhjp_right
```

これにより、GitHub Actionsが自動的に両側をビルドします。

---

## 9. ファームウェア構築手順

### 9.1 前提条件

- GitHubアカウント
- Gitクライアントインストール済み
- テキストエディタ（VS Code推奨）

### 9.2 zmk-configリポジトリの初期化

**手順**:

1. **ZMK公式テンプレートを使用**

   https://github.com/zmkfirmware/unified-zmk-config-template にアクセスし、「Use this template」→「Create a new repository」をクリック

2. **リポジトリ名設定**

   リポジトリ名: `zmk-config-hhkb-jp-split` (任意)

   Publicに設定（GitHub Actions無料枠を利用）

3. **ローカルにクローン**

   ```bash
   git clone https://github.com/<あなたのユーザー名>/zmk-config-hhkb-jp-split.git
   cd zmk-config-hhkb-jp-split
   ```

### 9.3 シールド定義ファイルの作成

**手順**:

1. **ディレクトリ作成**

   ```bash
   mkdir -p config/boards/shields/cleave_hhjp
   ```

2. **以下のファイルを作成**

   - `config/boards/shields/cleave_hhjp/Kconfig.shield`
   - `config/boards/shields/cleave_hhjp/Kconfig.defconfig`
   - `config/boards/shields/cleave_hhjp/cleave_hhjp.conf`
   - `config/boards/shields/cleave_hhjp/cleave_hhjp.dtsi`
   - `config/boards/shields/cleave_hhjp/cleave_hhjp_left.overlay`
   - `config/boards/shields/cleave_hhjp/cleave_hhjp_left.conf`
   - `config/boards/shields/cleave_hhjp/cleave_hhjp_right.overlay`
   - `config/boards/shields/cleave_hhjp/cleave_hhjp_right.conf`
   - `config/boards/shields/cleave_hhjp/cleave_hhjp.keymap`

3. **各ファイルの内容を記述**

   各ファイルの詳細は、本設計書の該当セクション（3.3節、4節、5節、6節）を参照し、ハードウェア仕様に基づいて記述します。

### 9.4 west.yml の設定

**手順**:

1. **config/west.yml を編集**

   zmk-rgbled-widget 依存関係を追加：

   ```yaml
   manifest:
     remotes:
       - name: zmkfirmware
         url-base: https://github.com/zmkfirmware
       - name: caksoylar
         url-base: https://github.com/caksoylar
     projects:
       - name: zmk
         remote: zmkfirmware
         revision: main
         import: app/west.yml
       - name: zmk-rgbled-widget
         remote: caksoylar
         revision: main
     self:
       path: config
   ```

### 9.5 build.yaml の設定

**手順**:

1. **build.yaml を編集**

   ```yaml
   include:
     - board: seeeduino_xiao_ble
       shield: cleave_hhjp_left
     - board: seeeduino_xiao_ble
       shield: cleave_hhjp_right
   ```

### 9.6 GitHub Actionsの有効化

**手順**:

1. **.github/workflows/build.yml を確認**

   zmk-configテンプレートから初期化した場合、すでに `build.yml` が存在します。内容を確認し、必要に応じてカスタマイズします。

2. **リポジトリにプッシュ**

   ```bash
   git add .
   git commit -m "Initial HHKB JP split keyboard configuration"
   git push origin main
   ```

3. **GitHub Actionsの実行確認**

   GitHubリポジトリの「Actions」タブでビルドが開始されることを確認します。

4. **ビルド成功後**

   「Actions」→最新のワークフロー実行→「Artifacts」からUF2ファイルをダウンロードできます。

### 9.7 リリース作成（オプション）

**手順**:

1. **Gitタグ作成**

   ```bash
   git tag v1.0.0
   git push origin v1.0.0
   ```

2. **GitHub Releasesに自動アップロード**

   GitHub Actionsワークフローが自動的にReleaseを作成し、UF2ファイルを添付します（build.ymlの設定による）。

---

## 10. ファームウェア書き込み手順

### 10.1 必要なもの

- 組み立て済みのキーボード（左右両側）
- USB Type-Cケーブル
- ビルド済みUF2ファイル（左右各1つ）

### 10.2 書き込み手順（左側）

1. **ブートローダーモード起動**

   - 左側キーボードの電源スイッチをONにする
   - USB Type-CケーブルでPCに接続
   - XIAOのリセットボタンを**ダブルクリック**
   - オレンジ色のLEDがゆっくり点滅開始（ブートローダーモード）

2. **USBマスストレージデバイスとして認識**

   - PCに「XIAO-SENSE」または「XIAO-nRF52」という名前のドライブがマウントされる

3. **UF2ファイルコピー**

   - `cleave_hhjp_left-seeeduino_xiao_ble-zmk.uf2` をドライブにドラッグ&ドロップ
   - 自動的に書き込みが開始され、完了後に自動でアンマウント

4. **書き込み完了**

   - 左側キーボードが自動的に再起動
   - User LEDが点滅開始（BLEアドバタイジング中）

### 10.3 書き込み手順（右側）

左側と同様の手順を右側キーボードでも実行します：

1. 右側キーボードの電源スイッチをONにする
2. USB Type-CケーブルでPCに接続
3. XIAOのリセットボタンを**ダブルクリック**
4. `cleave_hhjp_right-seeeduino_xiao_ble-zmk.uf2` をコピー
5. 自動的に再起動

### 10.4 ペアリング（完全ワイヤレス動作）

**重要**: このキーボードは**完全ワイヤレス設計**です。常時利用はすべてBluetooth接続で行います。

1. **左右接続（自動）**

   - 左右両側の電源をONにする
   - 左側（セントラル）が右側（ペリフェラル）を自動検出・接続
   - 接続完了後、User LEDが青色点滅（接続済み）

2. **ホストPC接続（Bluetooth）**

   - **USB接続を外す**（充電・ファームウェア更新が完了したら抜く）
   - PCのBluetooth設定を開く
   - 「Cleave HHJP」という名前のデバイスが表示される
   - ペアリング実行
   - 接続完了後、キーボードが**完全ワイヤレス**で使用可能

3. **複数デバイス切替**

   - Bluetoothレイヤー（Layer 2）で最大5台のデバイスを切替可能
   - `BT_SEL 0`〜`BT_SEL 4` でデバイス切替
   - 例: PC、Mac、タブレット、スマートフォン等を切り替えて使用可能

### 10.5 トラブルシューティング

| 症状 | 原因 | 解決方法 |
|------|------|----------|
| ブートローダーモードに入らない | リセットボタンのダブルクリック失敗 | ゆっくり2回押す（間隔0.5秒程度） |
| UF2コピー後もドライブが残る | 書き込み失敗 | 正しいUF2ファイルか確認、再度コピー |
| 左右が接続しない | ファームウェア不一致 | 左右両側に正しいUF2を書き込み直す |
| PCに接続できない | Bluetoothドライバー問題 | PCを再起動、Bluetoothをオフ/オン |
| キーが反応しない | マトリックス配線不良 | はんだ付け確認、ダイオード方向確認 |

---

## 11. 動作確認

### 11.1 初期動作確認チェックリスト

**完全ワイヤレス動作の確認**:

- [ ] 左側キーボード電源ON後、User LEDが点滅（アドバタイジング）
- [ ] 右側キーボード電源ON後、User LEDが点滅（アドバタイジング）
- [ ] 左右が自動接続し、User LEDが青色点滅（接続済み）
- [ ] **USB接続なし**でPCのBluetooth設定で「Cleave HHJP」が検出される
- [ ] **USB接続なし**でPCとペアリング完了後、全70キーが正常に入力される
- [ ] **完全ワイヤレス状態**でFnキー押下時のレイヤー切替が動作（F1-F12等）
- [ ] **完全ワイヤレス状態**でBluetoothレイヤーによるデバイス切替が動作
- [ ] USB充電時、充電LEDが赤色点灯（充電中、キーボード動作は停止してOK）
- [ ] USB充電完了後、充電LEDが緑色点灯（充電完了）

**ZMK Studio確認（USB接続時のみ）**:

- [ ] ZMK Studio (https://zmk.studio) でキーボードが検出される（USB接続必須）
- [ ] ZMK Studioでキーマップ変更が可能
- [ ] キーマップ変更後、USB接続を外しても設定が保持される

### 11.2 LED動作確認

| 条件 | 期待されるLED表示 |
|------|-------------------|
| 電源ON直後（バッテリー>80%） | 緑色点滅 |
| BLEアドバタイジング中 | 黄色点滅 |
| BLE接続済み | 青色点滅 |
| バッテリー残量<20% | 赤色点滅 |
| USB充電中 | 充電LED赤色点灯 |
| USB充電完了 | 充電LED緑色点灯 |

### 11.3 ZMK Studio動作確認（USB接続時のみ）

**注意**: ZMK StudioはUSB接続時のみ使用可能です。設定変更後は、USB接続を外して完全ワイヤレスで動作させることを推奨します。

1. ブラウザで https://zmk.studio を開く
2. **左側キーボードをUSB接続**（完全ワイヤレス動作中の場合は一時的にUSB接続）
3. 「Connect」ボタンをクリック
4. 「Cleave HHJP」が表示されることを確認
5. 接続後、70キーの物理レイアウトが表示されることを確認
6. 任意のキーをクリックし、別のキーコードに変更
7. 「Save」ボタンで保存
8. キーボードを再起動せずに、変更したキーが動作することを確認
9. **USB接続を外し、Bluetooth接続で変更が保持されていることを確認**

---

## 12. カスタマイズ・拡張

### 12.1 キーマップのカスタマイズ

**方法1: ZMK Studioで変更（推奨）**
- GUIで直感的に変更可能
- ファームウェア再ビルド不要

**方法2: keymapファイル編集**
- `config/boards/shields/cleave_hhjp/cleave_hhjp.keymap` を編集
- より高度なカスタマイズ（マクロ、コンボ等）が可能
- 編集後、Gitにコミット・プッシュしてGitHub Actionsで自動ビルド

### 12.2 レイヤーの追加

1. `cleave_hhjp.keymap` の `keymap` ノード内に新しいレイヤーを追加
2. `bindings` プロパティに70キー分のキーコードを定義
3. 既存レイヤーに新レイヤーへの切替キー（`&mo 3` 等）を配置

### 12.3 マクロの追加

ZMKのマクロ機能を使用して、複数キー入力を1キーで実行可能：

1. `keymap` ファイルに `macros` ノードを追加
2. マクロを定義（例: `ZMK_MACRO(hello, ...)`）
3. キーマップでマクロを割り当て（例: `&hello`）

### 12.4 コンボの追加

複数キー同時押しで別のキーを出力：

1. `keymap` ファイルに `combos` ノードを追加
2. コンボを定義（例: A+Sで Escを出力）
3. タイムアウト設定（デフォルト50ms）

### 12.5 スリープ時間の変更

`cleave_hhjp.conf` で以下を変更：

- `CONFIG_ZMK_IDLE_TIMEOUT`: アイドルタイムアウト（ミリ秒）
- `CONFIG_ZMK_IDLE_SLEEP_TIMEOUT`: スリープ移行時間（ミリ秒）

### 12.6 Bluetooth出力パワーの調整

分割通信の距離や安定性を調整：

- `CONFIG_BT_CTLR_TX_PWR_PLUS_8`: +8dBm（最大）
- `CONFIG_BT_CTLR_TX_PWR_PLUS_4`: +4dBm
- `CONFIG_BT_CTLR_TX_PWR_0`: 0dBm（デフォルト）

---

## 13. デバッグ・ログ出力

### 13.1 USB経由のログ出力

開発時のデバッグには、USB経由でログを出力可能です：

**設定**:
`cleave_hhjp.conf` に以下を追加：

```
CONFIG_ZMK_USB_LOGGING=y
```

**ログ確認方法**:

**macOS**:
```bash
screen /dev/tty.usbmodem* 115200
```

**Linux**:
```bash
screen /dev/ttyACM0 115200
```

**Windows**:
- Tera TermやPuTTY等のシリアルターミナルでCOMポートに接続

### 13.2 ログレベルの設定

より詳細なログを出力：

```
CONFIG_LOG=y
CONFIG_ZMK_LOG_LEVEL_DBG=y
```

---

## 14. リファレンス

### 14.1 公式ドキュメント

| 項目 | URL |
|------|-----|
| ZMK公式ドキュメント | https://zmk.dev/ |
| ZMK Studio | https://zmk.studio |
| Zephyr RTOS | https://docs.zephyrproject.org/ |
| Nordic nRF52840 | https://www.nordicsemi.com/Products/nRF52840 |
| Seeed XIAO nRF52840 | https://wiki.seeedstudio.com/XIAO_BLE/ |

### 14.2 関連リポジトリ

| 項目 | URL |
|------|-----|
| ZMK本体 | https://github.com/zmkfirmware/zmk |
| zmk-config テンプレート | https://github.com/zmkfirmware/unified-zmk-config-template |
| zmk-rgbled-widget | https://github.com/caksoylar/zmk-rgbled-widget |
