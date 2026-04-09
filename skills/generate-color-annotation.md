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

#### ドキュメント用アイコンのスワップ

各ステートの instance を `component.createInstance()` した直後、`appendChild` する前に、ネストされた `Icon Container` のアイコンを `_swap_icons`（ドキュメント専用）にスワップする。
これにより、`hasLeftIcon` / `hasRightIcon` を `true` のままにしているケースでも、点線プレースホルダーではなく実アイコンが表示される。

詳しいヘルパー定義は `generate-anatomy.md` の Step 4.5 を参照。要約：

```javascript
async function swapDocIcons(rootInstance) {
  const LEFT_ICON_ID = '5291:19589';   // _swap_icons / position=left (star)
  const RIGHT_ICON_ID = '5291:19602';  // _swap_icons / position=right (chevron)
  const iconContainers = rootInstance.findAll(
    n => n.type === 'INSTANCE' && n.name === 'Icon Container'
  );
  for (const ic of iconContainers) {
    const visibleRef = ic.componentPropertyReferences && ic.componentPropertyReferences.visible;
    const targetIconId = (visibleRef && visibleRef.startsWith('hasRightIcon'))
      ? RIGHT_ICON_ID
      : LEFT_ICON_ID;
    const iconPropKey = Object.keys(ic.componentProperties).find(k => /^icon(#|$)/.test(k));
    if (iconPropKey) ic.setProperties({ [iconPropKey]: targetIconId });
  }
}

// 各 instance 配置の流れ：
// const instance = component.createInstance();
// instance.setProperties({ state: 'default', ... });
// await swapDocIcons(instance);   ← ここでスワップ
// frame.appendChild(instance);
```

非表示のアイコンに対しては no-op になるので、`hasLeftIcon = false` を設定して隠している場合は何も起こらない。

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
