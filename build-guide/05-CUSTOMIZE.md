# CLEAVE HHJP カスタマイズガイド

[目次に戻る](README.md)

CLEAVE HHJPのキーマップやファームウェア設定を変更する方法をまとめます。日常的なキー割り当ての変更はZMK Studio、配布用の初期キーマップやZMK設定そのものを変更したい場合はリポジトリをフォークしてファームウェアをビルドします。

> [!NOTE]
> 初期キーマップのまま使う場合、このガイドの作業は必須ではありません。
> キー配置やファームウェア設定を変更したくなったときに参照してください。

## 目次

- [カスタマイズ方法の選び方](#カスタマイズ方法の選び方)
- [ZMK Studioで変更する](#zmk-studioで変更する)
- [フォークしてファームウェアを変更する](#フォークしてファームウェアを変更する)
- [変更後の書き込みと確認](#変更後の書き込みと確認)
- [トラブルシューティング](#トラブルシューティング)
- [参考リンク](#参考リンク)

## カスタマイズ方法の選び方

| 方法 | 向いている変更 | 特徴 |
|---|---|---|
| ZMK Studio | キー割り当ての変更、既存レイヤー内の編集 | ファームウェアの再ビルド不要。左側キーボードをUSB接続してZMK Studioで変更します。 |
| フォークして変更 | 初期キーマップ変更、レイヤー構成の変更、マクロやコンボの追加、スリープ時間やBluetooth設定の変更 | リポジトリ上の設定ファイルを編集し、GitHub ActionsでUF2ファイルを生成します。 |

### できることの比較

| 変更内容 | ZMK Studioで変更 | フォークして変更 |
|---|---|---|
| 単一キーの割り当て変更 | ◯ | ◯ |
| 既存レイヤー内の複数キーの調整 | ◯ | ◯ |
| ファームウェアに定義済みの動作を別のキーへ割り当て | ◯ | ◯ |
| 新しいレイヤーを初期状態として追加 |  | ◯ |
| 新しいマクロ、コンボ、独自behaviorを定義 |  | ◯ |
| 物理レイアウトやマトリックス定義を変更 |  | ◯ |
| スリープ時間、Bluetooth接続数、BLE出力などを変更 |  | ◯ |

ZMK Studioは、キーボードを使いながらキー割り当てを調整したい場合に向いています。一方で、ファームウェア側にまだ定義していない動作や設定を増やす場合は、リポジトリをフォークして変更します。

> [!IMPORTANT]
> ZMK Studioで保存した設定はキーボード本体の永続化領域に保存されます。
> その後に`cleave_hhjp.keymap`を変更したファームウェアを書き込んでも、ZMK Studioで保存した内容が優先される場合があります。
> ファームウェア側の初期キーマップに戻したい場合は、ZMK StudioのRestore Stock Settingsを実行するか、必要に応じて`settings_reset`用ファームウェアを書き込んでから左右用のUF2を書き込み直してください。

## ZMK Studioで変更する

以下の手順で、左側キーボードをZMK Studioに接続してキーマップを変更します。

> [!NOTE]
> CLEAVE HHJPではZMK Studioを左側キーボードで使用します。右側キーボードをUSB接続してもZMK Studioでは変更できません。

### 手順

1. 左側キーボードをUSB Type-CケーブルでPCに接続します。
2. [ZMK Studio](https://zmk.studio)を開きます。通常はブラウザ版を使用します。
3. `Connect`から`CLEAVE HHJP`を選択します。
4. キーマップを変更して保存します。
5. USBケーブルを抜き、Bluetooth接続で変更後のキーが動作することを確認します。

> [!NOTE]
> ZMK Studioにはブラウザ版とクライアント版があります。通常はブラウザ版で作業できますが、OSやブラウザのUSBシリアルポート権限、Web Serial APIの対応状況によって接続できない場合があります。その場合はクライアント版を使用してください。

## フォークしてファームウェアを変更する

リポジトリをフォークすると、自分用のコピーで設定ファイルを編集し、GitHub ActionsでCLEAVE HHJP用のUF2ファイルを生成できます。ローカルに専用のビルド環境を用意する必要はありません。

### 全体の流れ

| 段階 | やること |
|---|---|
| 1. Fork | GitHub上に自分用のリポジトリを作る |
| 2. Edit | キーマップや設定ファイルを変更する |
| 3. Build | GitHub ActionsでUF2ファイルを生成する |
| 4. Download | 生成された`firmware` artifactをダウンロードする |
| 5. Flash | 左右のXIAO nRF52840 PlusへUF2を書き込む |

GitHub Actionsは、GitHub上でファームウェアを自動ビルドする仕組みです。自分のPCにビルド環境を作らなくても、リポジトリに保存した変更からUF2ファイルを作成できます。

### 事前に用意するもの

- GitHubアカウント
- Webブラウザ
- UF2ファイルを書き込むためのUSB Type-Cケーブル
- ローカルで編集する場合は、Gitとテキストエディタ

GitHub上で直接ファイルを編集する場合は、Gitのコマンド操作は必須ではありません。GitHubのFork操作に慣れていない場合は、[GitHub公式のフォーク手順](https://docs.github.com/ja/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo)も参照してください。

### 最初に触るファイル

はじめてファームウェアを変更する場合は、まず`cleave_hhjp.keymap`だけを編集することを推奨します。

| 目的 | ファイル |
|---|---|
| キー割り当てを変更する | [`cleave_hhjp.keymap`](../config/boards/shields/cleave_hhjp/cleave_hhjp.keymap) |
| スリープ時間やBluetooth関連の設定を変更する | [`cleave_hhjp.conf`](../config/boards/shields/cleave_hhjp/cleave_hhjp.conf) |
| 左右の配線やキーマトリックスを変更する | [`cleave_hhjp_left.overlay`](../config/boards/shields/cleave_hhjp/cleave_hhjp_left.overlay) / [`cleave_hhjp_right.overlay`](../config/boards/shields/cleave_hhjp/cleave_hhjp_right.overlay) |

通常のキーマップ変更では、`.overlay`、`build.yaml`、`west.yml`は変更しません。

### 最小限の変更例

たとえば、左上の`Esc`キーを`Tab`キーに変更する場合は、`cleave_hhjp.keymap`の`default_layer`内にある以下の割り当てを変更します。

変更前:

```dts
&kp ESC
```

変更後:

```dts
&kp TAB
```

このように、`&kp`の後ろにあるキーコードを別のキーコードへ置き換えます。キーの数は増減させず、1つの割り当てを1つの割り当てに置き換えてください。

> [!IMPORTANT]
> `bindings`内の割り当てはCLEAVE HHJPの70キー分あります。
> 追加や削除で数が変わるとビルドに失敗します。
> `<`、`>`、`;`、`{`、`}`も消さないようにしてください。

### ビルドしてUF2をダウンロードする

1. GitHubで[CLEAVE-HHJPリポジトリ](https://github.com/Rimebeck/Cleave-HHJP)を開き、`Fork`から自分のアカウントへフォークします。
2. フォークしたリポジトリでActionsが無効になっている場合は、`Actions`タブからワークフローを有効化します。
3. GitHub上で直接編集する場合は、対象ファイルを開いて鉛筆アイコンから編集します。ローカルで編集する場合は、フォークしたリポジトリをcloneして`cleave_hhjp.keymap`などの設定ファイルを編集します。
4. GitHub上で直接編集した場合は`Commit changes`で保存します。ローカルで編集した場合はcommitしてフォークしたリポジトリへpushします。
5. `Actions`タブで`Build ZMK firmware`の最新の実行結果を開きます。
6. ビルドが成功したら、`Artifacts`から`firmware`をダウンロードします。
7. ダウンロードしたファイルを展開します。
8. [ファームウェアのダウンロード・書き込み・接続確認](02-FIRMWARE.md)と同じ手順で、生成されたUF2ファイルを左右それぞれのXIAO nRF52840 Plusへ書き込みます。

> [!NOTE]
> GitHub Actionsの画面やartifactのダウンロード方法は、ZMK公式の[Installing ZMK](https://zmk.dev/docs/user-setup)にも説明があります。

### 変更対象ファイルの早見表

| やりたいこと | 見るファイル | 難易度 | 備考 |
|---|---|---|---|
| キーを入れ替えたい | [`cleave_hhjp.keymap`](../config/boards/shields/cleave_hhjp/cleave_hhjp.keymap) | はじめて向け | まずはこのファイルだけを変更します。 |
| Fnレイヤーの割り当てを変えたい | [`cleave_hhjp.keymap`](../config/boards/shields/cleave_hhjp/cleave_hhjp.keymap) | はじめて向け | `fn_layer`内の割り当てを変更します。 |
| スリープ時間などを変えたい | [`cleave_hhjp.conf`](../config/boards/shields/cleave_hhjp/cleave_hhjp.conf) | 中級 | 数値や設定名の意味を確認してから変更します。 |
| 左右別の設定を変えたい | [`cleave_hhjp_left.conf`](../config/boards/shields/cleave_hhjp/cleave_hhjp_left.conf) / [`cleave_hhjp_right.conf`](../config/boards/shields/cleave_hhjp/cleave_hhjp_right.conf) | 中級 | 変更したい側のファイルだけを編集します。 |
| 配線やマトリックスを変えたい | [`cleave_hhjp_left.overlay`](../config/boards/shields/cleave_hhjp/cleave_hhjp_left.overlay) / [`cleave_hhjp_right.overlay`](../config/boards/shields/cleave_hhjp/cleave_hhjp_right.overlay) | 上級 | 通常のカスタマイズでは触りません。 |
| ビルド対象を変えたい | [`build.yaml`](../build.yaml) | 上級 | 生成するUF2の種類を変える場合に編集します。 |
| ZMKや関連モジュールの依存を変えたい | [`west.yml`](../config/west.yml) | 上級 | 通常のキーマップ変更では触りません。 |

### キーマップ変更の考え方

キーマップは`cleave_hhjp.keymap`の`keymap`ノード内で定義されています。CLEAVE HHJPでは標準の`default_layer`と、`Fn`キーで使う`fn_layer`が定義されています。

主な記法は以下の通りです。

| 記法 | 内容 |
|---|---|
| `&kp A` | `A`キーを送信します。 |
| `&kp ESC` | `Esc`キーを送信します。 |
| `&mo 1` | 押している間だけレイヤー1を有効にします。 |
| `&trans` | 下のレイヤーの動作をそのまま通します。 |
| `&bt BT_SEL 0` | Bluetoothプロファイル1を選択します。 |
| `&bt BT_CLR` | 現在のBluetoothプロファイルをクリアします。 |

各レイヤーの`bindings`には、CLEAVE HHJPの70キー分の割り当てを定義します。数が不足していたり、余っていたり、セミコロンや括弧が抜けているとビルドに失敗します。

キーコードやbehaviorを詳しく調べたい場合は、ZMK公式の[Keymaps & Behaviors](https://zmk.dev/docs/keymaps)と[List of Keycodes](https://zmk.dev/docs/keymaps/list-of-keycodes)を参照してください。

## 変更後の書き込みと確認

Actionsの`firmware` artifactには、以下のUF2ファイルが含まれます。

- `settings_reset-seeeduino_xiao_ble-zmk.uf2`
- `cleave_hhjp_left rgbled_adapter-seeeduino_xiao_ble-zmk.uf2`
- `cleave_hhjp_right rgbled_adapter-seeeduino_xiao_ble-zmk.uf2`

書き込み方法は[ファームウェアのダウンロード・書き込み・接続確認](02-FIRMWARE.md)と同じです。左側には左側用UF2、右側には右側用UF2を書き込みます。

ZMK Studioで保存した設定を消してファームウェア側の初期キーマップを確認したい場合や、左右の接続を最初からやり直す場合は、先に左右へ`settings_reset`を書き込み、その後に左右それぞれのUF2を書き込み直します。

**完了チェック**

- [ ] 左側に左側用UF2を書き込んだ
- [ ] 右側に右側用UF2を書き込んだ
- [ ] 必要に応じて`settings_reset`から書き込み直した
- [ ] PC側に古いBluetooth登録が残っている場合は削除した
- [ ] Bluetoothで`CLEAVE HHJP`に接続できる
- [ ] 変更したキーが意図した通りに入力される
- [ ] FnレイヤーやBluetoothプロファイル切り替えが動作する

## トラブルシューティング

### ZMK Studioに表示されない

- 左側キーボードをUSB接続しているか確認します。右側キーボードを接続してもZMK Studio用のデバイスとしては使用しません。
- USB Type-Cケーブルがデータ通信に対応しているか確認します。
- ブラウザでZMK StudioにUSBシリアルポートへのアクセスを許可しているか確認します。
- ブラウザ版で接続できない場合は、[ZMK Studioのダウンロードページ](https://zmk.studio/download)からクライアント版をインストールして試します。
- うまく接続できない場合は、USBケーブルを抜き差しして左側キーボードを再接続してから、再度`Connect`を試します。

### ファームウェアを書き込んだのにキーマップが変わらない

- ZMK Studioで保存したキーマップがキーボード本体に残っている可能性があります。ZMK StudioのRestore Stock Settingsを実行するか、`settings_reset`を書き込んでから左右用UF2を書き込み直します。
- 左右のUF2を取り違えていないか確認します。左側には`cleave_hhjp_left...uf2`、右側には`cleave_hhjp_right...uf2`を書き込みます。
- OS側に古いBluetooth情報が残っている場合は、`CLEAVE HHJP`の登録を削除してから再ペアリングします。

### GitHub Actionsが動かない

- フォークしたリポジトリでActionsが有効になっているか確認します。
- GitHub上で直接編集した場合は、`Commit changes`で保存できているか確認します。
- ローカルで編集した場合は、フォークしたリポジトリへpushできているか確認します。
- `Actions`タブで最新の`Build ZMK firmware`を開いているか確認します。

### ビルドが失敗する

- `cleave_hhjp.keymap`の各レイヤーに70キー分の`bindings`があるか確認します。
- `<`、`>`、`;`、`{`、`}`の抜けや余りがないか確認します。
- 使っているキーコードやbehavior名がZMKで定義されているか確認します。
- 新しいマクロやコンボを追加した場合は、参照名と定義名が一致しているか確認します。

### Bluetooth接続が不安定になった

- PC側のBluetooth設定から`CLEAVE HHJP`を削除して再ペアリングします。
- 現在選択中のBluetoothプロファイルをクリアしてから再ペアリングします。
- それでも改善しない場合は、左右両方へ`settings_reset`を書き込み、その後に左右用UF2を書き込み直します。

## 参考リンク

- [ZMK Studio](https://zmk.studio)
- [ZMK Studio ダウンロードページ](https://zmk.studio/download)
- [GitHub公式: リポジトリをフォークする](https://docs.github.com/ja/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo)
- [GitHub公式: GitHub Actions](https://docs.github.com/ja/actions)
- [ZMK公式: Installing ZMK](https://zmk.dev/docs/user-setup)
- [ZMK公式: Customizing ZMK](https://zmk.dev/docs/customization)
- [ZMK公式: Keymaps & Behaviors](https://zmk.dev/docs/keymaps)
- [ZMK公式: List of Keycodes](https://zmk.dev/docs/keymaps/list-of-keycodes)
- [ファームウェアのダウンロード・書き込み・接続確認](02-FIRMWARE.md)
- [ファームウェア設計書](../docs/firmware_design.md)

- [前: CLEAVE HHJP 完成後の使い方](04-USAGE.md)
- [目次に戻る](README.md)
