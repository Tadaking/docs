# スキル：トークンリファレンス更新

## 役割

Figma Console MCP で Variables を直接取得し、ドキュメント内のトークン値テーブルを再生成・更新する。
トークン値が変わったときにドキュメントを最新に保つために使う。

---

## インプット

| 項目 | 必須 | 説明 |
|---|---|---|
| Figma URL または node ID | ✅ | Variables が定義されているFigmaファイル |
| 更新対象MDXファイルパス | ✅ | 更新するドキュメントファイル |
| 更新対象トークングループ | — | 省略時は全トークンテーブルを更新（例: `color`, `size`, `typography`） |

---

## 実行手順

### Step 1: Variables の取得

Figma Console MCP を使って対象ファイルの Variables を直接取得する。
```
get_variable_defs(nodeId)
```

取得対象：
- トークン名・値・エイリアス（参照先）
- コレクション名・グループ構造
- モード（Light / Dark 等）ごとの値

### Step 2: 既存ドキュメントとの差分比較

対象MDXファイルを読み込み、現在のトークン値と取得した値を比較する。

- 値が変わったトークンをリストアップする
- 新規追加されたトークンをリストアップする
- 削除されたトークンをリストアップする

### Step 3: テーブルの再生成

対象MDXファイル内のトークンテーブルを新しい値で更新する。

- テーブルの形式・列構成は既存フォーマットを維持する
- 新規トークンは適切なグループに追加する
- 削除されたトークンは `<!-- REMOVED: {token-name} -->` コメントを残してから削除する

### Step 4: 変更サマリーの出力

更新後にターミナルへ変更内容を出力する：
```
更新されたトークン：
- color/surface/base/default: #FFFFFF → #F8F8F8
- color/on-surface/default: #1A1A1A → #111111

新規追加：
- color/surface/overlay: #00000014

削除：
- color/surface/deprecated

変更なし：42トークン
```

commit メッセージの雛形も合わせて出力する：
```
docs: update token values (color/surface/base/default, color/on-surface/default)
```

---

## フォールバック

| 状況 | 対処 |
|---|---|
| Variables の取得に失敗 | エラー内容を報告し、Figma Console MCPの接続を確認するよう促す |
| 既存テーブルの構造と一致しない | 上書きせず差分を報告し、手動確認を促す |
| モードの値が一致しない | 取得できたモードの値のみ更新し、不明なモードは `（要確認）` と記載 |
