# スキル：コンポーネントガイドライン生成

## トリガー条件

以下のいずれかに該当する場合、このスキルを使う：

- コンポーネントのガイドラインを新規作成する
- 既存のコンポーネントガイドラインに新しいプラットフォームタブを追加する

---

## インプット

| 項目 | 必須 | 説明 |
|---|---|---|
| Figma URL または node ID | ✅ | 対象コンポーネントのFigmaフレームまたはコンポーネントセット |
| 対象プラットフォーム | ✅ | `web` / `ios` / `android` / `all` |
| 出力ファイルパス | — | 省略時は `components/{component-name}.mdx` に自動決定 |

---

## Figma クエリ手順

以下の順番で実行する。

### Step 1: コンポーネント構造の取得
```
get_design_context(nodeId)
```

取得対象：
- component properties → バリアント定義として使う
- 子レイヤー名 → 構成要素（アイコン、ラベル、ボーダー等）として使う
- コンポーネントの description → 概要セクションの下書きとして使う

### Step 2: トークンの取得
```
get_variable_defs(nodeId)
```

取得対象：
- `COLOR` scope → カラートークンテーブルに使う
- `WIDTH_HEIGHT` / `GAP` / `CORNER_RADIUS` scope → サイズ・スペーシングテーブルに使う

### Step 3: `_guidelines` フレームの探索

コンポーネントの直下に `_guidelines` という名前のフレームを探す。

**見つかった場合：**
```
get_design_context(_guidelines の nodeId)
```
テキストレイヤー名で以下のセクションに振り分ける：

| レイヤー名 | 使用先 |
|---|---|
| `overview` | 概要セクション |
| `when-to-use` | バリアントの「いつ使う」 |
| `when-not-to-use` | バリアントの「いつ使わない」 |
| `do` | illustration-needed コメントの説明 |
| `dont` | illustration-needed コメントの説明 |
| `related` | 類似要素との使い分けセクション |

**見つからなかった場合：**
該当セクションを以下のTODOコメントに置き換えて続行する：
```md
<!-- TODO: _guidelines フレームを追加後に更新 -->
```

### Step 4: スクリーンショットの取得
```
get_screenshot(nodeId)
```

- `images/{component-name}-overview-light.png` として保存する
- ファイル内に `<!-- figma-frame: FILE_KEY/NODE_ID | バリアント一覧 -->` を記録する

---

## セクションマッピング

CLAUDE.md の `#### Overview タブの必須セクション` に従い、以下のルールで変換する。

| Figmaから取れるもの | MDXのセクション | なかった場合 |
|---|---|---|
| component description | `## 概要` | Claude が構造から推測して下書き + TODOコメント |
| component properties | `## バリアント` の各バリアント名 | そのまま列挙してTODOコメント |
| `_guidelines` > `when-to-use` | バリアントの「✅ いつ使う」 | TODOコメント |
| `_guidelines` > `when-not-to-use` | バリアントの「❌ いつ使わない」 | TODOコメント |
| `_guidelines` > `related` | `## 類似要素との使い分け` | セクションごとスキップ |
| `_guidelines` > `do` / `dont` | `<!-- illustration-needed: -->` | TODOコメント |
| `COLOR` scope の variable_defs | カラートークンテーブル | プレースホルダー行を生成 |
| `WIDTH_HEIGHT` / `GAP` scope | サイズ・スペーシングテーブル | プレースホルダー行を生成 |

### 画像プレースホルダーの判断ルール

- バリアントが2種類以上 → `<!-- figma-frame: ... | バリアント一覧 -->`
- サイズ展開あり → `<!-- figma-frame: ... | サイズ比較 -->`
- `_guidelines` に `do` / `dont` のテキストあり → `<!-- illustration-needed: [内容] -->`
- `_guidelines` がない → `<!-- illustration-needed: Do/Don't（_guidelines 追加後に更新） -->`

---

## 出力ルール

1. ファイルを `components/{component-name}.mdx` に作成する
2. CLAUDE.md の `#### コンポーネントガイドライン` テンプレートに従う
3. `docs.json` の `navigation` に以下の要領で追加する：
   - `"Foundation"` グループにはコンポーネントを追加しない
   - `"Components"` グループがなければ新規作成して追加する
4. プラットフォームと生成タブの対応は CLAUDE.md の以下のテーブルに従う：

   | 渡されたFigmaファイル | 生成・更新するタブ |
   |---|---|
   | Web Components ファイル | Overview（初回のみ）+ Web |
   | iOS Components ファイル | iOS |
   | Android Components ファイル | Android |

---

## フォールバック

| 状況 | 対処 |
|---|---|
| `_guidelines` フレームが存在しない | TODOコメントを挿入して続行。ドキュメント冒頭に `<!-- _guidelines フレーム未作成：追加後に再実行 -->` を追記 |
| トークンが取得できない | カラー・サイズテーブルをプレースホルダー行で生成。セル内に `（要確認）` と記載 |
| component description がない | コンポーネント名と構造から Claude が概要を推測して下書きし、TODOコメントを添える |
| スクリーンショット取得に失敗 | `<!-- figma-frame: 取得失敗 - node IDを手動で記入 -->` を挿入して続行 |
