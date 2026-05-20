# ビルドガイド完成 TODO

## 方針

Cleave HHJP の購入者が、部品確認から組み立て、ファームウェア書き込み、動作確認、トラブル対応まで迷わず進める状態にする。

比較の目安は Corne / Lily58 Pro / Keyball44 のような定番ビルドガイドで、以下の流れを満たすことをゴールにする。

- 部品確認
- 必要工具
- 事前準備
- 実装
- ケース・プレート・スイッチ取り付け
- ファームウェア書き込み
- 動作確認
- トラブルシューティング

## P0: 完成に必須

- [ ] `build-guide/01-ASSEMBLY.md` の XIAO nRF52840 Plus の実装手順を詳しくする
  - [ ] 取り付け向き
- [ ] `build-guide/03-CASE.md` を完成させる
  - 滑り止めの取り付け位置を追加する
  - [ ] 3D プリント条件を追加する
  - [ ] ケース完成写真を追加する
- [ ] `models/` に配布用 3D モデルを追加する、または未配置なら `README.md` / `build-guide/03-CASE.md` の案内を修正する
- [ ] `build-guide/02-FIRMWARE.md` のリセットボタン画像 TODO を解消する
- [ ] `build-guide/04-USAGE.md` に組み立て完了後の動作確認チェックリストを追加する
  - [ ] USB 接続時の挙動
  - [ ] 充電 LED の確認

## P1: 分かりやすさ改善

- [ ] `build-guide/01-ASSEMBLY.md` の部品表を、キット同梱品・購入者が用意する部品・任意オプションに分ける
- [ ] `build-guide/03-CASE.md` にスタビライザーが必要な MX 構成を補足する
- [ ] `build-guide/01-ASSEMBLY.md` にショットキーダイオードの左右別の向きが分かる拡大写真を追加する
- [ ] `build-guide/01-ASSEMBLY.md` / `build-guide/03-CASE.md` の各工程に「完了状態」と「よくある失敗」を追加する
- [ ] `build-guide/02-FIRMWARE.md` にトラブルシューティングを追加する
  - [ ] ブートローダーモードに入らない
  - [ ] UF2 コピー後にドライブが消えない
  - [ ] Bluetooth に表示されない
  - [ ] 左右が接続しない
  - [ ] 一部のキーが反応しない
- [ ] `build-guide/04-USAGE.md` に ZMK Studio でのキーマップ変更方法への導線を追加する

## P2: 仕上げ

- [ ] `build-guide/04-USAGE.md` に完成後の使い方を追加する
  - [ ] 電源スイッチ
  - [ ] Bluetooth デバイス切り替え
  - [ ] ペアリングリセット
  - [ ] 充電方法
- [ ] `build-guide/` の写真サイズと向きを揃える
- [ ] `README.md` / `build-guide/README.md` に問い合わせ先や不具合報告先を追加する
