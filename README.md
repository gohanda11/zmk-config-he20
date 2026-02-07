# HE20 - Hall Effect キーボード

XIAO nRF52840 + SN74LV4051A x3 + GH39FKSW による Hall Effect キーボードの ZMK ファームウェア設定。

## ハードウェア構成

| 要素 | 仕様 |
|------|------|
| MCU | Seeed XIAO nRF52840 |
| Hall Effect スイッチ | GH39FKSW |
| アナログマルチプレクサ | SN74LV4051A x 3 |
| トラックボールセンサー | PMW3610 ([zmk-pmw3610-driver](https://github.com/gohanda11/zmk-pmw3610-driver)) |
| ドライバ | [zmk-feature-hall-effect](https://github.com/gohanda11/zmk-feature-hall-effect) (フォーク版) |

## キーレイアウト

22 スイッチ位置を全て定義。未実装のキーは `&none` を割り当てる。

```
┌──────┬──────┬──────┬──────┬──────┐
│ HE1  │ HE2  │ HE3  │ HE4  │ HE5  │  Row 0
├──────┼──────┼──────┼──────┼──────┤
│ HE6  │ HE7  │ HE8  │ HE9  │ HE10 │  Row 1
├──────┼──────┼──────┼──────┼──────┤
│ HE11 │ HE12 │ HE13 │ HE14 │ HE15 │  Row 2
├──────┼──────┼──────┼──────┼──────┤
│ HE16 │ HE17 │ HE18 │ HE19 │ HE20 │  Row 3
└──────┴──────┴──────┴──────┴──────┘
┌─────────────┐      ┌─────────────┐
│  HE21 (2U)  │      │  HE22 (2U)  │  Row 4
└─────────────┘      └─────────────┘
```

Row 3 の 1U キーと Row 4 の 2U キーは物理的に入れ替えて使用できる。
取り付けていないキーには `&none` を設定する。

## トラックボール

PMW3610 センサーを J1 コネクタ経由で接続。

| 信号 | ピン | GPIO |
|------|------|------|
| CS | D0 | P0.02 |
| SDIO (MOSI/MISO) | D4 | P0.04 |
| SCLK | D5 | P0.05 |
| MOTION (IRQ) | D6 | P1.11 |

- automouse-layer: MOUSE (layer 1) - トラックボール操作時に自動切替
- scroll-layers: SCROLL (layer 5) - スクロールモード

## ビルド方法

GitHub Actions で自動ビルド。push すると `.github/workflows/build.yml` が実行される。

## ファイル構成

```
zmk-config-he20/
├── .github/workflows/build.yml
├── build.yaml
├── zephyr/module.yml
├── config/
│   ├── west.yml              # ZMK + zmk-feature-hall-effect モジュール定義
│   ├── he20.keymap            # キーマップ (7レイヤー)
│   └── he20.json              # 物理レイアウト定義 (keymap-editor 用)
└── boards/shields/he20/
    ├── Kconfig.shield
    ├── Kconfig.defconfig
    ├── he20.conf              # Kconfig 設定
    ├── he20.overlay           # Devicetree (kscan, レイアウト, transform)
    └── he20.zmk.yml           # シールドメタデータ
```
