# スキル：Anatomy フレーム生成

## 役割

コンポーネントの構成要素に番号マーカーを付けたAnatomyフレームをFigmaに生成し、
スクリーンショットをMDXに挿入する。

`generate-component-doc.md` からサブスキルとして呼び出されるほか、単独でも使用できる。

---

## インプット

| 項目 | 必須 | 説明 |
|---|---|---|
| Figma URL または node ID | ✅ | 対象コンポーネントのノード |
| 出力先MDXファイルパス | ✅ | Anatomyフレームの挿入先 |

---

## 実行手順

### Step 1: コンポーネント構造の取得
```
get_design_context(nodeId)
```

- 子レイヤーを走査し、構成要素（アイコン、ラベル、ボーダー、背景等）を洗い出す
- 各要素の名称・役割をリストアップする

### Step 2: Anatomyフレームの生成

Figma Console MCP の書き込み機能を使って、対象コンポーネントの隣にAnatomyフレームを生成する。

生成内容：
- コンポーネントのコピーを配置
- 各構成要素に番号マーカー（①②③…）を付与
- フレーム名：`{ComponentName}/Anatomy`

### Step 3: スクリーンショットの取得と保存
```
get_screenshot(anatomyFrameNodeId)
```

- `images/{component-name}-anatomy-light.png` として保存する

### Step 4: MDXへの挿入

対象MDXファイルの `## Anatomy` セクションに以下を挿入する：
```md
<!-- figma-frame: FILE_KEY/NODE_ID | Anatomy -->

| # | 要素名 | 役割 |
|---|---|---|
| 1 | （要素名） | （役割の説明） |
| 2 | （要素名） | （役割の説明） |
```

---

## フォールバック

| 状況 | 対処 |
|---|---|
| Figmaへの書き込みに失敗 | `<!-- TODO: Anatomyフレームを手動で作成後に更新 -->` を挿入して続行 |
| 構成要素が取得できない | テーブルをプレースホルダーで生成し `（要確認）` と記載 |
