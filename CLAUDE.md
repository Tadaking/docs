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
├── quickstart.mdx         # クイックスタートガイド
├── development.mdx        # ローカル開発ガイド
├── colors.mdx             # カラーガイドライン（Foundation）
├── essentials/            # Mintlifyの使い方ガイド（テンプレート由来）
├── ai-tools/              # AIツール連携ガイド
├── api-reference/         # APIリファレンス（テンプレート由来）
├── images/                # 画像ファイル
├── logo/                  # ロゴ（light.svg / dark.svg）
└── snippets/              # 再利用可能なコンテンツ断片
```

> **注:** `foundation/` や `components/` ディレクトリはまだ作成されていない。現状のカラーガイドライン（`colors.mdx`）はルート直下に配置されている。Foundation/Components向けのドキュメントが増えてきた段階で、ディレクトリを作成してファイルを移動することを検討する。

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
            "pages": ["index", "quickstart", "development"]
          },
          {
            "group": "Foundation",
            "pages": ["colors"]
          }
        ]
      },
      {
        "tab": "API reference",
        "groups": [
          {
            "group": "API documentation",
            "pages": ["api-reference/introduction"]
          }
        ]
      }
    ]
  }
}
```

新しいFoundationガイドラインは `"Foundation"` グループに、コンポーネントガイドラインは新しいグループを作って追加する。

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
1. プラットフォームに特化した使い方やベストプラクティスとDo, Don'tなど
2. カラートークン
3. サイズ・スペーシング
4. インタラクションステート
```

- カラートークン: 各バリアント × 各ステートのマッピングをテーブルで示す。
- サイズ・スペーシング: そのプラットフォームのトークンを使って記載する
- インタラクションステート: default / hover（Web）/ pressed / focused / disabled の挙動。focused と disabled（opacity: 0.4）は必ず記載する

---

### トークン選定の優先順位

トークンを提案・記載する際は、以下の優先順位を守る：

1. **Component Tokens** を最優先で探す（button, input, choice, table 等）
2. 該当がなければ **Lightness-Mode Tokens** から選ぶ（surface, on-surface, border, accent, feedback 等）
3. **Theme Tokens（primitive値）は例外的なケースのみ**。色に意味がなく置き換えても体験が変わらない場合に限る

Theme Tokensを使う場合は、理由を明記し、ダークモード対応が自前で必要な旨を注記する。

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

### スキルの呼び出し関係

`generate-component-doc` は以下をサブスキルとして呼び出す：
- `generate-anatomy`
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
| `color-chip-wrapper` | カラーチップの外枠（クリックでトークン名コピー） | `colors.mdx` |
| `color-chip` | チップの色面（light/darkモード切替対応） | `colors.mdx` |
| `color-chip-copied` | コピー完了時のアウトライン強調 | `colors.mdx` |
| `color-chip-check` | コピー完了時の ✓ アイコン | `colors.mdx` |
| `do-dont-grid` | Do/Don't の2カラム比較グリッド | `typography.mdx` |
| `do-card` / `dont-card` | Do/Don't の各カード（緑/赤ボーダー） | `typography.mdx` |
| `do-dont-label` | ✅/❌ のラベル行 | `typography.mdx` |
| `do-dont-caption` | Do/Don't の説明キャプション | `typography.mdx` |

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
| `ColorChip` | `colors.mdx` 冒頭（インライン） | カラートークンのチップ表示（クリックでコピー） |

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

### Figma MCP
- コンポーネントの構造、バリアント、適用トークンを読み取るのに使う
- スクリーンショットを `images/` にエクスポートして使う

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
- `colors.mdx` — カラーガイドライン（Theme/Lightness-Mode/Componentトークンを含む）。`ColorChip` コンポーネント（冒頭にインライン定義）の実装パターンとして参照すること。
- `typography.mdx` — タイポグラフィガイドライン。Overview / Web タブは完成済み。iOS / Android は該当 Figma ファイル追加後に更新予定。`do-dont-grid` などカスタム CSS の実装パターンの先例として参照すること。
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
3. commit & push する

### 既存ガイドラインを更新する場合
1. Mintlify MCPで現行の内容を確認する
2. 変更箇所を更新する
3. commit & push する

### トークン値を更新する場合
1. Figma Variables のJSONエクスポートを受け取る
2. 該当セクションのテーブルを再生成する
3. 変更されたトークンがあれば差分を commit メッセージに記載する
4. commit & push する
