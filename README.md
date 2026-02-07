# HE20 - Hall Effect キーボード

XIAO nRF52840 + SN74LV4051A x3 + GH39FKSW による Hall Effect キーボードの ZMK ファームウェア設定。

## ハードウェア構成

| 要素 | 仕様 |
|------|------|
| MCU | Seeed XIAO nRF52840 |
| Hall Effect スイッチ | GH39FKSW |
| アナログマルチプレクサ | SN74LV4051A x 3 |
| ドライバ | [zmk-feature-hall-effect](https://github.com/gohanda11/zmk-feature-hall-effect) (フォーク版) |

## キーレイアウト

デフォルトは `transform_2u_right` (1U 1U 1U 2U) の 19 キー構成。

```
┌──────┬──────┬──────┬──────┬──────┐
│ HE1  │ HE2  │ HE3  │ HE4  │ HE5  │  Row 0
├──────┼──────┼──────┼──────┼──────┤
│ HE6  │ HE7  │ HE8  │ HE9  │ HE10 │  Row 1
├──────┼──────┼──────┼──────┼──────┤
│ HE11 │ HE12 │ HE13 │ HE14 │ HE15 │  Row 2
├──────┼──────┼──────┬─────────────┤
│ HE16 │ HE17 │ HE18 │   HE22 (2U) │  Row 3
└──────┴──────┴──────┴─────────────┘
```

## 底面レイアウトの選択 (4種類)

Row 3 は以下の 4 パターンから選択可能。

| レイアウト名 | Row 3 構成 | キー数 |
|-------------|-----------|-------|
| `default_transform` | 1U, 1U, 1U, 1U, 1U | 20 |
| `transform_2u_left` | 2U, 1U, 1U, 1U | 19 |
| `transform_2u_right` | 1U, 1U, 1U, 2U | 19 (デフォルト) |
| `transform_2u_both` | 2U, 1U, 2U | 18 |

```
1U 1U 1U 1U 1U              2U 1U 1U 1U
┌────┬────┬────┬────┬────┐   ┌─────────┬────┬────┬────┐
│HE16│HE17│HE18│HE19│HE20│   │  HE21   │HE18│HE19│HE20│
└────┴────┴────┴────┴────┘   └─────────┴────┴────┴────┘

1U 1U 1U 2U (default)       2U 1U 2U
┌────┬────┬────┬─────────┐   ┌─────────┬────┬─────────┐
│HE16│HE17│HE18│  HE22   │   │  HE21   │HE18│  HE22   │
└────┴────┴────┴─────────┘   └─────────┴────┴─────────┘
```

### 変更方法 1: ZMK Studio (ランタイム切り替え)

ビルド不要。キーボードを USB 接続し、ブラウザから ZMK Studio を開く。

1. https://zmk.studio/ にアクセス
2. キーボードを接続
3. 画面上のレイアウト選択 UI から希望のレイアウトを選ぶ
4. 選択は自動的に NVS に保存され、電源を切っても維持される

### 変更方法 2: overlay を直接編集

`boards/shields/he20/he20.overlay` の `chosen` ノードを変更する。

```dts
/ {
    chosen {
        zmk,physical-layout = &he20_layout_2u_both;  // 使いたいレイアウト
    };
};
```

使用可能な値:

- `&he20_layout_1u` — 1U 1U 1U 1U 1U
- `&he20_layout_2u_left` — 2U 1U 1U 1U
- `&he20_layout_2u_right` — 1U 1U 1U 2U (デフォルト)
- `&he20_layout_2u_both` — 2U 1U 2U

変更後にビルドが必要。

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
│   └── he20.json              # 物理レイアウト定義 (keymap-editor / ZMK Studio 用)
└── boards/shields/he20/
    ├── Kconfig.shield
    ├── Kconfig.defconfig
    ├── he20.conf              # Kconfig 設定
    ├── he20.overlay           # Devicetree (kscan, レイアウト, transform)
    └── he20.zmk.yml           # シールドメタデータ
```
