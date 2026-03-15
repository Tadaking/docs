# スキル：カラーアノテーション生成

## 役割

コンポーネントの各要素×各ステートに適用されているカラートークンをFigmaにアノテーションとして生成し、
スクリーンショットをMDXのカラートークンセクションに挿入する。

`generate-component-doc.md` からサブスキルとして呼び出されるほか、単独でも使用できる。

---

## インプット

| 項目 | 必須 | 説明 |
|---|---|---|
| Figma URL または node ID | ✅ | 対象コンポーネントのノード |
| 対象プラットフォーム | ✅ | `web` / `ios` / `android` |
| 出力先MDXファイルパス | ✅ | アノテーション画像の挿入先 |

---

## 実行手順

### Step 1: トークンの取得
```
get_variable_defs(nodeId)
```

- `COLOR` scope のトークンを全て取得する
- 要素名・ステート・トークン名の対応表を作成する

### Step 2: アノテーションフレームの生成

Figma Console MCP の書き込み機能を使って、対象コンポーネントの隣にアノテーションフレームを生成する。

生成内容：
- ステートごと（default / hover / pressed / focused / disabled）にコンポーネントを並べる
- 各要素からトークン名を引き出す吹き出しを付与
- フレーム名：`{ComponentName}/Color Annotation`

### Step 3: スクリーンショットの取得と保存
```
get_screenshot(annotationFrameNodeId)
```

- `images/{component-name}-color-annotation-light.png` として保存する

### Step 4: MDXへの挿入

対象MDXファイルの `## カラートークン` セクションに以下を挿入する：
```md
<!-- figma-frame: FILE_KEY/NODE_ID | カラーアノテーション -->
```

その下にトークンマッピングテーブルを生成する：
```md
| 要素 | Default | Hover | Pressed | Focused | Disabled |
|---|---|---|---|---|---|
| （要素名） | `token-name` | `token-name` | `token-name` | `token-name` | opacity: 0.4 |
```

---

## フォールバック

| 状況 | 対処 |
|---|---|
| Figmaへの書き込みに失敗 | アノテーション画像なしでトークンテーブルのみ生成して続行 |
| トークンが取得できない | テーブルをプレースホルダーで生成し `（要確認）` と記載 |
