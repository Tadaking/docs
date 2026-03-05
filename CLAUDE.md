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

#### Foundation ガイドライン（Colors, Typography 等）
```
1. Overview（概要・設計思想）
2. Usage（使い方・原則）
3. Token values（トークン値テーブル）
```

#### コンポーネントガイドライン
```
1. 概要 — 目的と使用場面
2. 構成要素 — 含まれるパーツ（アイコン、ラベル、ボーダー等）
3. バリアント — サイズ、種類の組み合わせ
4. カラートークン割り当て — 各パーツ × 各ステートのマッピング
5. インタラクションステート — default, hover, pressed, focused, disabled
6. アクセシビリティ — キーボード操作、スクリーンリーダー、コントラスト
7. 使い分けガイド — 類似コンポーネントとの選択基準
```

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

### ダークモード

- ダークモードの値に言及する場合は「WIP（作業中）」であることを必ず付記する

## 言語・トーン

- 日本語で執筆する
- トークン名はコード上の名称をそのまま使う（例: `color/surface/base/default`）
- 「〜の問題がある」ではなく「〜というトレードオフがある」のように、判断材料として提示する書き方をする
- 突き放さず伴走者のような言葉遣いを心がける

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
- `colors.mdx` — カラーガイドライン（Theme/Lightness-Mode/Componentトークンを含む）

## ワークフロー

### 新しいガイドラインを書く場合
1. Figma MCPでコンポーネント/トークン情報を読み取る
2. テンプレートに沿って `.md` または `.mdx` ファイルを作成する
3. `docs.json` の `navigation.tabs[].groups[]` 内の該当グループにページパスを追加する
4. commit & push する

### 既存ガイドラインを更新する場合
1. Mintlify MCPで現行の内容を確認する
2. 変更箇所を更新する
3. commit & push する

### トークン値を更新する場合
1. Figma Variables のJSONエクスポートを受け取る
2. 該当セクションのテーブルを再生成する
3. 変更されたトークンがあれば差分を commit メッセージに記載する
4. commit & push する
