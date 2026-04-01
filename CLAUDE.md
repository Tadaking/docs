# CLAUDE.md — Garage Design System Documentation

このリポジトリは Garage Design System のガイドラインドキュメントを管理し、Mintlify でホスティングしています。

## リポジトリ構造

```
/
├── CLAUDE.md              # この指示書（Claude Code向け）
├── AGENTS.md              # Mintlify AI向けの指示書
├── docs.json              # Mintlify設定（ナビゲーション、テーマ等）
├── .mintignore            # Mintlifyビルドから除外するファイル
├── index.mdx              # トップページ
├── foundation/            # Foundation ガイドライン
│   ├── colors.mdx         # カラーガイドライン
│   ├── typography.mdx     # タイポグラフィガイドライン
│   ├── size.mdx           # サイズガイドライン
│   └── shadows.mdx        # シャドウガイドライン
├── components/            # コンポーネントガイドライン
│   └── button.mdx         # Button ガイドライン
├── ai-tools/              # AIツール連携ガイド
├── essentials/            # Mintlifyの使い方ガイド（テンプレート由来）
├── images/                # 画像ファイル
├── logo/                  # ロゴ（light.svg / dark.svg）
└── snippets/              # 再利用可能なコンテンツ断片
```

## Mintlify の基本ルール

### ファイル形式
- 通常のドキュメントは `.md` を使う
- Mintlifyカスタムコンポーネント（`<Tabs>`, `<Card>` 等）を使うときだけ `.mdx`
- `.mdx` では波括弧がJSX式として解釈されるため、トークン参照（`{color.xxx}`）を含むファイルは原則 `.md` にする

### フロントマター（必須）
すべてのドキュメントファイルの先頭に記述する：

```md
---
title: "ページタイトル"
description: "1行の説明文"
---
```

### ナビゲーション登録
新しいファイルを追加したら、必ず `docs.json` の `navigation` にパスを追加する。パスは拡張子なしのファイルパス。

現在の `docs.json` はタブベースの構造を使用している：

```json
{
  "navigation": {
    "tabs": [
      {
        "tab": "Guides",
        "groups": [
          {
            "group": "Getting started",
            "pages": ["index"]
          },
          {
            "group": "Foundation",
            "pages": [
              "foundation/colors",
              "foundation/typography",
              "foundation/size",
              "foundation/shadows"
            ]
          },
          {
            "group": "Components",
            "pages": ["components/button"]
          }
        ]
      }
    ]
  }
}
```

新しいFoundationガイドラインは `"Foundation"` グループに `foundation/` プレフィックス付きで、コンポーネントガイドラインは `"Components"` グループに `components/` プレフィックス付きで追加する。

## 記法ルール

### 波括弧のエスケープ
Figmaトークンの参照先を表す `{color.neutral-alpha.5}` のような表記は、必ずバックティック（`` ` ``）で囲む。

- ✅ `{color.neutral-alpha.5}`
- ❌ {color.neutral-alpha.5}

テーブル内でも同様：

```md
| トークン | 参照先 |
|---|---|
| color.surface-container.whisper | `{color.neutral-alpha.5}` |
```

### テーブル
- ヘッダー行とセパレーター行を必ず含める
- 空セルは避け、該当なしの場合は `-` や `—` を入れる

### 画像
- `images/` ディレクトリに配置（ロゴは `logo/` に配置済み）
- ファイル名は `{カテゴリ}-{内容}-{light|dark}.png` 形式（例: `colors-do-dont-surface-light.png`）
- Figmaのフレームから画像を使う場合は、ファイル内にコメントでフレームIDを残す：
  `<!-- figma-frame: FILE_KEY/NODE_ID -->`

### .mintignore
`drafts/` ディレクトリや `*.draft.mdx` ファイルはビルドから除外される。下書き段階のドキュメントはこれらの命名規則に従う。

## ガイドライン執筆ルール

### 文書構造テンプレート
コンテンツの先頭に該当figmaファイルのURLをコメントアウトで記しておく

#### Foundation ガイドライン（Colors, Typography 等）
```
1. Overview（概要・設計思想）
2. Usage（使い方・原則）
3. Token values（トークン値テーブル）
```

#### コンポーネントガイドライン
コンポーネントガイドラインは**仕様書ではなくガイドライン**として書く。「何ができるか」より「いつ・なぜ使うか/使わないか」を優先する。

##### テキスト量の原則

ガイドラインのビジュアル（Figmaフレーム、Do/Don't画像）が主役で、テキストは添え書き。テキストだけで完結させようとしない。

- 1セクションあたり本文は4行以内を目安にする
- 4行を超えそうなら、削るか画像の注釈（キャプション）に移す
- Do/Don't はビジュアルで示し、テキストは1行の理由のみ添える

#### 画像プレースホルダー

ビジュアルで説明した方が伝わる箇所には、テキストの代わりにプレースホルダーを挿入する。
コンポーネント自体の見た目を示すならFigmaフレームで、使い方・ルールの説明には説明用イラストが必要なケースがある。

**パターンA: Figmaフレームで足りる場合**（コンポーネント自体の見た目・バリアント比較など）
Figma MCPでnode IDを取得して記述する。

```
<!-- figma-frame: FILE_KEY/NODE_ID | 内容の説明 -->
```

例：
```
<!-- figma-frame: Pvlx7A6oDLlnPF8MQYFDcq/4148-4978 | バリアント一覧（Primary / Secondary / Neutral / Ghost） -->
<!-- figma-frame: Pvlx7A6oDLlnPF8MQYFDcq/4148-4979 | サイズ比較（xl〜sm） -->
```

**パターンB: 説明用イラストが必要な場合**（Do/Don't・配置ルール・組み合わせパターンなど）
Figmaフレームだけでは伝わりにくい場合は、どんな画像を作ると良いかを記述する。

```
<!-- illustration-needed: 内容の説明 -->
```

例：
```
<!-- illustration-needed: Primary + Secondary の正しい並べ方（Do）と Primary + Primary のNG例（Don't）の比較 -->
<!-- illustration-needed: ボタンをモーダルフッターに配置する場合の右寄せレイアウト例 -->
```

画像プレースホルダーを使う場面：
- バリアントの見た目の違い → パターンA
- サイズ・スペーシングの図解 → パターンA
- Do/Don't の比較 → パターンB（説明用イラスト）
- 組み合わせパターンの例示 → パターンB（説明用イラスト）
- 配置・アライメントのルール → パターンB（説明用イラスト）

#### タブ構造

コンポーネントガイドラインは **Overview / Web / iOS / Android** の4タブで構成する。
各タブを独立したセクションとして `.md` に記述する。

```
## Overview
（全プラットフォーム共通のガイドライン）

## Web
（Web固有の仕様）

## iOS
（iOS固有の仕様）

## Android
（Android固有の仕様）
```

**Figmaファイルと生成対象の対応：**

| 渡されたFigmaファイル | 生成・更新するタブ |
|---|---|
| Web Components ファイル | Overview（初回のみ）+ Web |
| iOS Components ファイル | iOS |
| Android Components ファイル | Android |

- Overview は最初に Web ファイルから生成する。以後、iOS・Android を追加しても Overview は原則変更しない
- すでに Overview が存在する場合は、該当プラットフォームのタブのみ追加・更新する

#### Overview タブの必須セクション（順序を守る）

```
1. 概要
2. 類似要素との使い分け（該当する場合）
3. Anatomy
4. バリアント
5. 組み合わせパターン
6. ラベル・コンテンツの書き方
7. アクセシビリティ
```

#### 3. Anatomy

コンポーネントを構成する要素に番号マーカーを付けた図と、各要素の名称・役割をテーブルで示す。

`skills/generate-anatomy.md` を使ってFigmaにAnatomyフレームを生成し、スクリーンショットをMDXに挿入する。
```md
<!-- figma-frame: FILE_KEY/NODE_ID | Anatomy -->

| # | 要素名 | 役割 |
|---|---|---|
| 1 | （要素名） | （役割の説明） |
| 2 | （要素名） | （役割の説明） |
```

テキストの説明は不要。図とテーブルのみで構成する。

#### 1. 概要

1〜2文のみ。コンポーネントの目的だけを書く。構造やプロパティの説明は不要。

#### 2. 類似要素との使い分け

Button と Link、Modal と Drawer のように、混同しやすい要素が存在する場合に設ける。
「アクションを実行する → Button」「別の場所に移動する → Link」のような、判断基準を明示する。

#### 3. バリアント

各バリアントに必ず以下の3点を含める。説明の羅列だけで終わらせない。

```
{バリアント名}
（1〜2文の説明）

✅ いつ使う
- 具体的なシナリオを箇条書き

❌ いつ使わない
- 具体的な理由を添えて箇条書き
```

バリアントの「説明」に書くこと：
- そのバリアントが持つ**視覚的な重要度**と**意味的な役割**
- 他のバリアントとの**相対的な位置づけ**

バリアントの説明に書かなくていいこと：
- 色・背景・テキスト色などの外見の詳細（Web/iOS/Androidタブに書く）
- コンポーネントの内部構造（実装上の説明は除外）

#### 4. 組み合わせパターン

複数のバリアントを並べる場合の推奨・非推奨の組み合わせを書く。
「Primary + Secondary はOK」「Primary + Primary はNG」のような、具体的なルールを示す。

3つ以上のアクションが並ぶ場合の対処法も含める。

#### 5. ラベル・コンテンツの書き方

ラベルテキストのルールを書く。

- 動詞＋名詞の形式（例:「保存」→「変更を保存」、「削除」→「アカウントを削除」）
- 避けるべき表現（「クリック」「こちら」などの曖昧な表現）
- 文字数の目安（ある場合）

#### 6. アクセシビリティ

- キーボード操作（どのキーでアクティベートするか）
- スクリーンリーダー対応（aria属性）
- コントラスト（APCA基準で評価、具体的な組み合わせを記載）

#### Web / iOS / Android タブの必須セクション

```
1. プラットフォームに特化した使い方やベストプラクティスとDo, Don't
2. Size・State・Shape ビジュアル（画像 + サイズ・スペーシングトークンテーブル）
3. カラートークン（使用トークンの一覧テーブル）
```

- Size・State・Shape ビジュアル: バリアントビジュアル画像とサイズ・スペーシングトークンテーブルをセットで掲載する。インタラクションステート（default / hover（Web）/ pressed / focused / disabled）の挙動も含める。focused と disabled（opacity: 0.4）は必ず記載する
- カラートークン: 各バリアント × 各ステートのマッピングをテーブルで示す。**Figma の Component コレクション変数（`color/button/*`、`size/Control/web/*` 等）は記載しない**（後述）

---

### トークン選定の優先順位

トークンを提案・記載する際は、以下の優先順位を守る：

1. **LM（Lightness-Mode）Tokens** を最優先で選ぶ（surface, on-surface, border, accent, feedback 等）
2. 該当がなければ **Pattern コレクション（Size/Control/\*）** のサイズトークンを使う
3. **Theme Tokens（primitive値）は例外的なケースのみ**。色に意味がなく置き換えても体験が変わらない場合に限る

Theme Tokensを使う場合は、理由を明記し、ダークモード対応が自前で必要な旨を注記する。

#### ⚠️ Figma Component コレクション変数はコードに存在しない

Figma Variables に **Component コレクション**（`color/button/*`、`size/Control/web/*` 等）が存在するが、これらは**デザイン作業専用のエイリアス**であり、Panda CSS の実装には存在しない。

MDX に記載するトークン（カラートークンテーブル・サイズ・スペーシングテーブル）には必ずエイリアスを辿った先の **LM / Pattern コレクションのトークン名**を使うこと。

| ❌ 記載してはいけない（Figma Component コレクション） | ✅ 記載すべきLMトークン |
|---|---|
| `color/button/primary/background/default` | `color/accent-container/primary/medium` |
| `color/button/neutral/background/hover` | `color/surface-container/bold` |
| `size/Control/web/height/md` | `Size/Control/md/size` |
| `size/button/web/height/xl` | `Size/Control/xl/size` |

カラートークンテーブルの冒頭には必ず以下の注記を入れること：

```md
> 実装時はこのテーブルの LM（Lightness-Mode）トークンを直接使うこと。FigmaのComponent Variablesはデザイン作業用の参照であり、コードには存在しない。
```

### アクセシビリティ

- コントラスト評価はAPCA基準（WCAG 2.xではない）
- UIテキスト: Lc 60以上
- 本文テキスト: Lc 75以上

### Disabled状態

無効状態ではカラートークンを変更せず、`opacity: 0.4`（40%）を一律適用する。

### インタラクションの濃淡変化

- ライトモード：濃くすることで強調
- ダークモード：明るくすることで強調
- 基準色に応じて1-2段階変化させる

## 言語・トーン

- 日本語で執筆する
- トークン名はコード上の名称をそのまま使う（例: `color/surface/base/default`）
- 「〜の問題がある」ではなく「〜というトレードオフがある」のように、判断材料として提示する書き方をする
- 突き放さず伴走者のような言葉遣いを心がける

### ガイドライン文体のルール

読んでいる人（デザイナー・エンジニア）を伴走して導くような文体で書く。

**セクション冒頭・説明文：**
- 命令形（「〜してください」）ではなく、提案・案内形（「〜を使いましょう」「〜が適しています」）
- 理由や文脈を添える（「なぜなら〜」ではなく「〜のため、〜が効果的です」のように自然につなげる）
- 例：❌「Primaryを使ってください」→ ✅「最も目立たせたいアクションには Primary を選びましょう」

**箇条書き内：**
- 簡潔に事実・基準を書けばよい。文体を整える必要はない
- 例：「フォームの送信ボタン」「画面内に1つだけ配置」

**`_guidelines` フレームのメモをMDXに変換するとき：**
- メモの箇条書きはそのまま箇条書きとして使う
- バリアントの説明文（1〜2文）はガイドライン文体に整える
- 元のメモのニュアンス・意図を変えない

## エージェントスキル

コンポーネント・Foundationドキュメントの生成・更新は `skills/` 内のスキルファイルを使う。

### スキル一覧

| ファイル | 役割 | 出力先 |
|---|---|---|
| `generate-component-doc.md` | コンポーネントガイドラインの新規生成 | Mintlify MDX |
| `generate-anatomy.md` | AnatomyフレームをFigmaに生成してMDXに挿入 | Figma + MDX |
| `generate-color-annotation.md` | カラーアノテーションをFigmaに生成してMDXに挿入 | Figma + MDX |
| `generate-foundation-doc.md` | FoundationページのMDX生成 | Mintlify MDX |
| `generate-token-reference.md` | Figma Console MCPでVariablesを取得しトークンテーブルを更新 | Mintlify MDX |
| `sync-platform-tab.md` | 既存ドキュメントにiOS/Androidタブを追加 | Mintlify MDX |
| `generate-screen-reader.md` | アクセシビリティセクションを生成 | Mintlify MDX |
| `generate-variants-visual.md` | Size・State・ShapeバリアントをFigmaに生成してMDXに挿入 | Figma + MDX |

### スキルの呼び出し関係

`generate-component-doc` は以下をサブスキルとして呼び出す：
- `generate-anatomy`
- `generate-variants-visual`
- `generate-color-annotation`
- `generate-screen-reader`

## カスタムコンポーネント・CSS

Mintlify の組み込みコンポーネントで表現できない UI は、カスタム CSS または MDX 内の React コンポーネントで実装する。

### CSSファイルの場所と読み込み

- ファイル：`/custom.css`（リポジトリルート直下）
- Mintlify がルート直下の `custom.css` を自動で読み込むため、`docs.json` への追記は不要

### 既存のカスタムスタイル（`custom.css`）

| クラス名 | 用途 | 使用例 |
|---|---|---|
| `color-chip-wrapper` | カラーチップの外枠（クリックでトークン名コピー） | `foundation/colors.mdx` |
| `color-chip` | チップの色面（light/darkモード切替対応） | `foundation/colors.mdx` |
| `color-chip-copied` | コピー完了時のアウトライン強調 | `foundation/colors.mdx` |
| `color-chip-check` | コピー完了時の ✓ アイコン | `foundation/colors.mdx` |
| `do-dont-grid` | Do/Don't の2カラム比較グリッド | `foundation/typography.mdx` |
| `do-card` / `dont-card` / `caution-card` | Do/Don't/Caution カード（上部カラーバー + 薄背景） | `foundation/typography.mdx` |
| `do-dont-label` | ✅/❌/⚠️ のラベル行 | `foundation/typography.mdx` |
| `do-dont-caption` | Do/Don't の説明キャプション | `foundation/typography.mdx` |
| `do-dont-preview` | プレビュー画像エリア | `foundation/typography.mdx` |
| `type-preview` | タイポグラフィプレビューブロック | `foundation/typography.mdx` |
| `type-preview-sample-jp` / `type-preview-sample-en` | JP/ENサンプルテキスト（実フォント描画） | `foundation/typography.mdx` |
| `type-preview-name` / `type-preview-font` / `type-preview-specs` | プレビューのラベル・フォント名・スペック行 | `foundation/typography.mdx` |
| `type-preview-select` | 行間切り替えドロップダウン | `foundation/typography.mdx` |
| `typescale-switcher` | Standard/Comfortable タブ切り替え | `foundation/typography.mdx` |

### 新しいカスタムスタイルを追加するとき

1. `/custom.css` にクラスを追記する（ダークモード対応も必ずセットで書く）
2. 上のテーブルに追記する
3. 初めて使う `.mdx` ファイルを「使用例」列に記録する

### ダークモード対応の書き方
```css
/* ライトモード（デフォルト） */
.your-class { ... }

/* ダークモード */
.dark .your-class { ... }
```

## 再利用可能なコンポーネント（snippets）

### ⚠️ Mintlify の制約

`snippets/` は `export const` を含むコンポーネント定義には対応していない。
`export const` を snippets に書くと hydration エラーが発生するため、**React コンポーネントは各 `.mdx` ファイルの冒頭にインラインで定義する**。

### 現在のコンポーネント定義場所

| コンポーネント名 | 定義場所 | 用途 |
|---|---|---|
| `ColorChip` | `foundation/colors.mdx` 冒頭（インライン） | カラートークンのチップ表示（クリックでコピー） |

### 別ページで同じコンポーネントを使いたい場合

snippets への切り出しはできないため、該当の `.mdx` ファイル冒頭に同じ `export const` 定義をコピーして使う。
コンポーネントのソースは定義元ファイル（上のテーブルの「定義場所」）を参照すること。

### 実装パターンの選択基準

| やりたいこと | 実装方法 |
|---|---|
| Do/Don't の並列比較 | `do-dont-grid` クラス（`custom.css`） |
| カラーチップの表示 | `ColorChip`（定義元からコピー） |
| インタラクティブな動作（上記以外） | 該当 `.mdx` の冒頭に `export const` でインライン定義 |
| 上記以外で Mintlify 組み込みで表現できる | `<Card>`, `<Tabs>`, `<Note>`, `<Warning>` 等を使う |

## MCP 連携

### ⚠️ 重要：使用するMCPの指定

Figma の操作には必ず **Figma Console MCP** を使用する。
リモートMCP（`mcp.figma.com` 等）は使用しない。

- ✅ 正しい：Figma Console MCP（ローカル接続、読み書き両対応）
- ❌ 間違い：リモートMCP（読み取り専用、書き込み不可）

Figma Console MCP が接続されていない場合は作業を中断し、接続を確認するよう報告する。

### Figma Console MCP
- コンポーネントの構造、バリアント、適用トークンを読み取るのに使う
- スクリーショットを `images/` にエクスポートして使う
- Figmaへの書き込み（フレーム生成・テキスト編集・インスタンス配置等）もこのMCPで行う

#### 画像の書き出し

画像の書き出しには必ず `figma_get_component_image` を使う。
以下の方法は使わない：
- `get_screenshot`（不安定・破損が多い）
- `figma_execute` での `exportAsync` + base64（トークン消費が大きく遅い）

### `_guidelines` フレームの構造

コンポーネントと同じページに `_guidelines` という名前のフレームを作成する。
レイヤー構造は以下に従う：
```
_guidelines
├── overview          ← コンポーネントの概要（テキスト）
├── related           ← 類似要素との使い分け（テキスト）
├── variants
│   ├── {variant-name}    ← バリアント名（例: primary, secondary）
│   │   ├── when-to-use       ← いつ使う（テキスト）
│   │   └── when-not-to-use   ← いつ使わない（テキスト）
│   └── ...
└── combinations
    ├── do-1
    │   ├── frame     ← Figmaフレーム（画像として書き出す）
    │   └── caption   ← キャプションテキスト
    ├── do-2
    │   └── ...
    ├── dont-1
    │   ├── frame
    │   └── caption
    ├── caution-1
    │   ├── frame
    │   └── caption
    └── ...
```

命名ルール：
- `do-{n}` / `dont-{n}` / `caution-{n}` の `{n}` は 1 始まりの連番
- `frame` レイヤーは画像として書き出してMDXに挿入する
- `caption` レイヤーはテキストレイヤーとして読み取りMDXのキャプションに使う

### Mintlify MCP（newmo.mintlify.app/mcp）
- 既存ドキュメントの検索・参照に使う
- 新しいガイドラインと既存の命名・構造の整合性を確認する

## 現状のリポジトリについて

このリポジトリはMintlifyのスターターテンプレートをベースにしている。以下のファイルはテンプレート由来で、Garage Design Systemのコンテンツに順次置き換えていく予定：

- `essentials/` — Mintlifyの使い方ガイド（markdown.mdx, code.mdx, images.mdx 等）
- `api-reference/` — サンプルAPIリファレンス
- `quickstart.mdx`, `development.mdx` — Mintlifyセットアップ手順
- `index.mdx` — テンプレートのトップページ

Garage Design System固有のコンテンツ：
- `foundation/colors.mdx` — カラーガイドライン（Theme/Lightness-Mode/Componentトークンを含む）。`ColorChip` コンポーネント（冒頭にインライン定義）の実装パターンとして参照すること。
- `foundation/typography.mdx` — タイポグラフィガイドライン。Overview / Web タブは完成済み。iOS / Android は該当 Figma ファイル追加後に更新予定。`do-dont-grid` などカスタム CSS の実装パターンの先例として参照すること。
- `custom.css` — カスタムスタイル定義（ルート直下）。Mintlify が自動で読み込む。新しいスタイルを追加する際は `## カスタムコンポーネント・CSS` セクションのテーブルも更新すること。

## ワークフロー

### 新しいガイドラインを書く場合

**コンポーネントガイドラインの場合：**
`skills/generate-component-doc.md` の手順に従って自動生成する。

**Foundationガイドラインの場合：**
CLAUDE.md の `#### Foundation ガイドライン` テンプレートに従って手動で作成する。

共通手順：
1. ファイルを作成する
2. `docs.json` の `navigation.tabs[].groups[]` 内の該当グループにパスを追加する
3. `index.mdx` の該当セクションにページへのリンクを追加する
4. 更新履歴にエントリを追加する（基準は後述）
5. commit & push する

### 既存ガイドラインを更新する場合
1. Mintlify MCPで現行の内容を確認する
2. 変更箇所を更新する
3. 更新履歴にエントリを追加する（基準は後述）
4. commit & push する

### 更新履歴の管理

`index.mdx` 末尾の「更新履歴」セクションに Mintlify の `<Update>` コンポーネントでエントリを追加する。
新しいエントリは既存エントリの**上**に追加する（時系列降順）。

```mdx
<Update label="YYYY-MM-DD" description="変更の要約（1行）">
  変更の詳細を1〜2文で記述する。
</Update>
```

#### 載せる変更

- 新しいガイドライン（Foundation / Component）の追加
- 既存ガイドラインへのセクション追加・大幅な書き換え
- 新しいプラットフォームタブ（iOS / Android）の追加
- 新しいバリアントの追加に伴うドキュメント更新

#### 載せない変更

- 誤字・脱字の修正
- トークン値の微修正（参照先の変更のみ等）
- 文言の微調整・リライト
- 画像の差し替え（内容が変わらないもの）
- CLAUDE.md や docs.json など設定ファイルのみの変更

### トークン値を更新する場合
1. Figma Variables のJSONエクスポートを受け取る
2. 該当セクションのテーブルを再生成する
3. 変更されたトークンがあれば差分を commit メッセージに記載する
4. commit & push する

### コンポーネントのデザインが変更された場合
````
`components/{component-name}.mdx` を更新してください。
Figmaのデザインが変更されたため、以下をFigma Console MCPで再取得して更新します。

- Figma: {URL}
- 更新対象: {Anatomy / バリアントプレビュー / Size・State・Shape / カラートークン / すべて}
- 既存の `{ComponentName}/*` フレームはFigmaから削除してから再生成してください
````

### Variables の値が変更された場合
````
`{file}.mdx` のトークンテーブルを更新してください。
@skills/generate-token-reference.md の手順に従い、Figma Console MCPで最新のVariablesを取得して差分を反映してください。

- Figma: {URL}
- 更新対象セクション: {Primitive / Semantic / すべて}
````

### プラットフォームタブを追加する場合（iOS/Android Figmaができたとき）
````
`components/{component-name}.mdx` に {ios / android} タブを追加してください。
@skills/sync-platform-tab.md の手順に従ってください。

- Figma: {URL}
- 対象プラットフォーム: {ios / android}
````

### `_guidelines` フレームを追加・更新した場合
````
`components/{component-name}.mdx` の以下のセクションを `_guidelines` フレームの内容で更新してください。
Figmaを再読み取りして反映します。既存の画像・ビジュアルはそのまま維持してください。

- Figma: {URL}
- 更新対象: {概要 / バリアントのwhen-to-use・when-not-to-use / 組み合わせパターン / すべて}
````

### 新しいバリアントが追加された場合
````
`components/{component-name}.mdx` を更新してください。
新しいバリアントが追加されたため、以下を再生成してください。

- Figma: {URL}
- 更新対象: バリアントプレビュー・Stateビジュアル・カラートークン
- 既存の `{ComponentName}/Preview/*` `{ComponentName}/State` フレームはFigmaから削除してから再生成してください
````
