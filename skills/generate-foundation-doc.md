# スキル：Foundation ガイドライン生成

## 役割

Foundation ページ（Spacing / Elevation / Motion 等）の MDX を新規生成する。
`foundation/colors.mdx` と同じタブ構成（Overview / Primitive / Semantic / ...）で出力する。

---

## インプット

| 項目 | 必須 | 説明 |
|---|---|---|
| Figma URL または node ID | ✅ | Variables が定義されているFigmaファイル |
| ドキュメント種別 | ✅ | `spacing` / `elevation` / `motion` 等 |
| 出力ファイルパス | — | 省略時は `{種別}.mdx` に自動決定 |

---

## 実行手順

### Step 1: Variables の取得

Figma Console MCP を使って対象ファイルの Variables を直接取得する。
```
get_variable_defs(nodeId)
```

取得後、以下の構造に分類する：

| 分類 | 判断基準 | タブ |
|---|---|---|
| Primitive | エイリアスを持たない（直接値を持つ）トークン | Primitive タブ |
| Semantic | 別のトークンへのエイリアスを持つトークン | Semantic タブ |
| Component | コンポーネント名を含むトークン（例: `button/...`） | 必要なら追加タブ |

### Step 2: コレクション構造の把握

Variable コレクション名・グループ名をドキュメントの見出し構造として使う。

### Step 3: スクリーンショットの取得
```
get_screenshot(nodeId)
```

- `images/{種別}-overview-light.png` として保存する
- 戻り値を直接使わず、必ずファイルとして保存してからパスを参照する

### Step 4: MDXの生成

CLAUDE.md の Mintlify ルールに従い、以下のタブ構成で `.mdx` ファイルを生成する。

#### フロントマター
```md
---
title: "{種別名}"
description: "{1行の説明文}"
---

{/* figma: {FigmaファイルURL} */}
```

#### Overview タブ
```md
<Tab title="Overview">
  ## Quicklook

  - （このFoundationの設計思想を2〜3行で説明）

  ## トークンの階層

  | 層 | 役割 |
  |---|---|
  | Primitive | 基礎となる値の定義 |
  | Semantic | 用途に応じた意味付け |

  ![Overview](/images/{種別}-overview-light.png)
</Tab>
```

#### Primitive タブ

Primitive トークンを値テーブルで列挙する。
`foundation/colors.mdx` の Theme タブのフォーマットに準拠する。
```md
<Tab title="Primitive">
  ## {グループ名}

  | トークン | 値 |
  |---|---|
  | `{token-name}` | `{value}` |
</Tab>
```

#### Semantic タブ

Semantic トークンをエイリアス先と値のテーブルで列挙する。
`foundation/colors.mdx` の Lightness-Mode タブのフォーマットに準拠する。
```md
<Tab title="Semantic">
  ## {グループ名}

  | トークン | 参照先 | 値 |
  |---|---|---|
  | `{token-name}` | `{alias-target}` | `{resolved-value}` |
</Tab>
```

#### 追加タブ（必要な場合）

Component トークンが存在する場合は追加タブを生成する：
```md
<Tab title="Component">
  ## {コンポーネント名}

  | トークン | 参照先 | 値 |
  |---|---|---|
  | `{token-name}` | `{alias-target}` | `{resolved-value}` |
</Tab>
```

### Step 5: docs.json への追加

`docs.json` の `"Foundation"` グループに生成したファイルパスを追加する。

---

## フォールバック

| 状況 | 対処 |
|---|---|
| Variables の取得に失敗 | エラー内容を報告し、Figma Console MCPの接続を確認するよう促す |
| Primitive / Semantic の分類が不明確 | コレクション名・グループ名を手がかりに分類し、不明なものは Primitive として扱い `（要確認）` と記載 |
| スクリーンショット取得に失敗 | `<!-- figma-frame: 取得失敗 - node IDを手動で記入 -->` を挿入して続行 |
