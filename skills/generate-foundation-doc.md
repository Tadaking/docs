# スキル：Foundation ガイドライン生成

## 役割

Colors / Typography / Spacing 等のFoundationページのMDXを生成する。

---

## インプット

| 項目 | 必須 | 説明 |
|---|---|---|
| Figma URL または node ID | ✅ | 対象FoundationのFigmaフレーム |
| ドキュメント種別 | ✅ | `colors` / `typography` / `spacing` / `elevation` 等 |
| 出力ファイルパス | — | 省略時は `{種別}.mdx` に自動決定 |

---

## Figma クエリ手順

### Step 1: トークン定義の取得
```
get_variable_defs(nodeId)
```

- ドキュメント種別に応じて取得するscopeを切り替える：

| 種別 | 取得するscope |
|---|---|
| colors | `COLOR` |
| typography | `FONT_SIZE` / `FONT_WEIGHT` / `LINE_HEIGHT` |
| spacing | `GAP` / `WIDTH_HEIGHT` |
| elevation | `EFFECT` |

### Step 2: 構造の取得
```
get_design_context(nodeId)
```

- セクション構成・グループ名を取得し、ドキュメントの見出し構造として使う

### Step 3: スクリーンショットの取得
```
get_screenshot(nodeId)
```

- `images/{種別}-overview-light.png` として保存する

---

## セクションマッピング

CLAUDE.md の `#### Foundation ガイドライン` テンプレートに従う：
```
1. Overview（概要・設計思想）
2. Usage（使い方・原則）
3. Token values（トークン値テーブル）
```

既存ファイルがある場合（`colors.mdx` / `typography.mdx` 等）は上書きせず、
更新対象のセクションのみ差し替える。

---

## フォールバック

| 状況 | 対処 |
|---|---|
| トークンが取得できない | テーブルをプレースホルダーで生成し `（要確認）` と記載 |
| 既存ファイルがある | 差分のみ更新し、既存コンテンツを保持する |
