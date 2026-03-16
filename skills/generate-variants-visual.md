# スキル：バリアントビジュアル生成

## 役割

コンポーネントのSize・State・Shapeの各バリアントを横並びにしたFigmaフレームを生成し、
スクリーンショットをMDXのバリアントセクションに挿入する。

`generate-component-doc.md` からサブスキルとして呼び出されるほか、単独でも使用できる。

---

## インプット

| 項目 | 必須 | 説明 |
|---|---|---|
| 対象コンポーネントの Figma URL または node ID | ✅ | |
| 出力先MDXファイルパス | ✅ | |

---

## 実行手順

### Step 1: プロパティの取得
```
get_design_context(nodeId)
```

component properties から以下を取得する：

| プロパティ名 | 種別 | 用途 |
|---|---|---|
| `size` | variant | Sizeセクションの横並び |
| `state` | variant | Stateセクションの横並び（一部） |
| `focused` | boolean | Stateセクション（focused状態） |
| `isDisabled` | boolean | Stateセクション（disabled状態） |
| `full-radius` | boolean | Shapeセクション |

取得できたプロパティのみセクションを生成する。存在しないプロパティはスキップする。

---

### Step 2: Size フレームの生成（`size` プロパティがある場合）

`figma_execute` を使い、sizeの値を小さい順に横並びにしたフレームを生成する。
```javascript
// 既存フレームの右端を取得し、重ならない位置に配置する
const existingFrames = figma.currentPage.children;
const maxX = existingFrames.reduce((max, node) => Math.max(max, node.x + node.width), 0);

// 横並びフレームの生成パターン
const frame = figma.createFrame();
frame.name = '{ComponentName}/Size';
frame.layoutMode = 'HORIZONTAL';
frame.itemSpacing = 24;
frame.paddingTop = frame.paddingBottom = 32;
frame.paddingLeft = frame.paddingRight = 32;
frame.fills = [{ type: 'SOLID', color: { r: 0.97, g: 0.97, b: 0.97 } }];
frame.cornerRadius = 12;
frame.clipsContent = false;
frame.x = maxX + 100;
frame.y = 0;

// sizeの値を小さい順に並べる（sm → md → lg → xl）
const sizeOrder = ['2xs', 'xs', 'sm', 'md', 'lg', 'xl', '2xl'];
const sortedSizes = availableSizes.sort((a, b) => sizeOrder.indexOf(a) - sizeOrder.indexOf(b));

await figma.loadFontAsync({ family: 'Inter', style: 'Regular' });

for (const size of sortedSizes) {
  const instance = component.createInstance();

  // boolean propertiesのうち、装飾系（アイコン等）をオフにする
  const booleanProps = Object.entries(instance.componentProperties)
    .filter(([key, prop]) =>
      prop.type === 'BOOLEAN' &&
      prop.value === true &&
      !['focused', 'isDisabled', 'isPending'].includes(key)
    );
  for (const [key] of booleanProps) {
    instance.setProperties({ [key]: false });
  }

  instance.setProperties({ size });

  // インスタンス本体のclipsContentのみオフにする（子は触らない）
  if ('clipsContent' in instance) instance.clipsContent = false;

  // インスタンス内の label/text プロパティにサイズ名を反映する（#nodeId サフィックス対応）
  const textProps = Object.keys(instance.componentProperties)
    .filter(key => /^(label|text)(#|$)/i.test(key));
  if (textProps.length > 0) {
    instance.setProperties({ [textProps[0]]: size });
  }

  // wrapperは使わず、インスタンスを直接frameに追加する
  if ('clipsContent' in instance) instance.clipsContent = false;
  frame.appendChild(instance);
}
```

---

### Step 3: State フレームの生成

`variant` × `intent` の全組み合わせを行として、state 5種を列として並べるグリッドを生成する。
sizeは `md`（存在しない場合は最小サイズ）に固定する。

#### 3-1: 組み合わせの列挙

`get_design_context` で取得した component properties から：
- `variant` の全値を取得する
- `intent` の全値を取得する（存在しない場合は `['default']` として扱う）
- 全組み合わせをリストアップする

#### 3-2: フレームの生成
```javascript
const outerFrame = figma.createFrame();
outerFrame.name = '{ComponentName}/State';
outerFrame.layoutMode = 'VERTICAL';
outerFrame.itemSpacing = 16;
outerFrame.paddingTop = outerFrame.paddingBottom = 32;
outerFrame.paddingLeft = outerFrame.paddingRight = 32;
outerFrame.fills = [{ type: 'SOLID', color: { r: 0.97, g: 0.97, b: 0.97 } }];
outerFrame.cornerRadius = 12;
outerFrame.clipsContent = false;

const states = [
  { label: 'Default',  props: { state: 'default', focused: false, isDisabled: false } },
  { label: 'Hover',    props: { state: 'hover' } },
  { label: 'Pressed',  props: { state: 'pressed' } },
  { label: 'Focused',  props: { state: 'default', focused: true } },
  { label: 'Disabled', props: { state: 'default', isDisabled: true } },
];

for (const { variantValue, intentValue } of combinations) {
  // 1行 = variant × intent の横並びフレーム
  const rowFrame = figma.createFrame();
  rowFrame.layoutMode = 'HORIZONTAL';
  rowFrame.itemSpacing = 16;
  rowFrame.fills = [];
  rowFrame.clipsContent = false;

  for (const { label, props } of states) {
    const instance = component.createInstance();

    // プロパティ設定
    const setProps = { size: 'md', variant: variantValue };
    if (intentValue !== 'default') setProps.intent = intentValue;
    Object.assign(setProps, props);

    // 存在するプロパティのみ設定する（存在しないプロパティは無視）
    const validProps = Object.fromEntries(
      Object.entries(setProps).filter(([key]) => key in instance.componentProperties)
    );
    instance.setProperties(validProps);

    // 装飾系boolean（focused/isDisabled/isPending以外）をオフにする
    const booleanProps = Object.entries(instance.componentProperties)
      .filter(([key, prop]) =>
        prop.type === 'BOOLEAN' &&
        prop.value === true &&
        !['focused', 'isDisabled', 'isPending'].includes(key)
      );
    for (const [key] of booleanProps) {
      instance.setProperties({ [key]: false });
    }

    // ラベルプロパティにステート名を反映（#nodeId サフィックス対応）
    const textProps = Object.keys(instance.componentProperties)
      .filter(key => /^(label|text)(#|$)/i.test(key));
    if (textProps.length > 0) {
      instance.setProperties({ [textProps[0]]: label });
    }

    // clipsContentをオフ
    if ('clipsContent' in instance) instance.clipsContent = false;

    rowFrame.appendChild(instance);
  }

  outerFrame.appendChild(rowFrame);
}

// 既存フレームと重ならない位置に配置
const existingFrames = figma.currentPage.children;
const maxX = existingFrames.reduce((max, node) => Math.max(max, node.x + node.width), 0);
outerFrame.x = maxX + 100;
outerFrame.y = 0;
```

#### 3-3: ラベル列の追加（オプション）

各行の左端に `variant-intent` のラベルテキストを追加する：
```javascript
const rowLabel = figma.createText();
await figma.loadFontAsync({ family: 'Inter', style: 'Regular' });
rowLabel.characters = intentValue !== 'default'
  ? `${variantValue} / ${intentValue}`
  : variantValue;
rowLabel.fontSize = 11;
rowLabel.fills = [{ type: 'SOLID', color: { r: 0.5, g: 0.5, b: 0.5 } }];
rowFrame.insertChild(0, rowLabel); // 行の先頭に挿入
```

---

### Step 4: Shape フレームの生成（`full-radius` プロパティがある場合）

`full-radius=false`（角丸）と `full-radius=true`（フルラディウス）を横並びにする。

```javascript
// 既存フレームの右端を取得し、重ならない位置に配置する
const existingFrames = figma.currentPage.children;
const maxX = existingFrames.reduce((max, node) => Math.max(max, node.x + node.width), 0);

const frame = figma.createFrame();
frame.name = '{ComponentName}/Shape';
frame.layoutMode = 'HORIZONTAL';
frame.itemSpacing = 24;
frame.paddingTop = frame.paddingBottom = 32;
frame.paddingLeft = frame.paddingRight = 32;
frame.fills = [{ type: 'SOLID', color: { r: 0.97, g: 0.97, b: 0.97 } }];
frame.cornerRadius = 12;
frame.clipsContent = false;
frame.x = maxX + 100;
frame.y = 0;

await figma.loadFontAsync({ family: 'Inter', style: 'Regular' });

const shapes = [
  { label: 'Default',     fullRadius: false },
  { label: 'Full radius', fullRadius: true  },
];

for (const s of shapes) {
  const instance = component.createInstance();

  // boolean propertiesのうち、装飾系（アイコン等）をオフにする
  const booleanProps = Object.entries(instance.componentProperties)
    .filter(([key, prop]) =>
      prop.type === 'BOOLEAN' &&
      prop.value === true &&
      !['focused', 'isDisabled', 'isPending'].includes(key)
    );
  for (const [key] of booleanProps) {
    instance.setProperties({ [key]: false });
  }

  instance.setProperties({ 'full-radius': s.fullRadius });

  // インスタンス本体のclipsContentのみオフにする（子は触らない）
  if ('clipsContent' in instance) instance.clipsContent = false;

  // インスタンス内の label/text プロパティにラベル名を反映する（#nodeId サフィックス対応）
  const textProps = Object.keys(instance.componentProperties)
    .filter(key => /^(label|text)(#|$)/i.test(key));
  if (textProps.length > 0) {
    instance.setProperties({ [textProps[0]]: s.label });
  }

  // wrapperに入れる（ラベルテキストは不要、インスタンス内のlabelプロパティに反映済み）
  const wrapper = figma.createFrame();
  wrapper.layoutMode = 'NONE';
  wrapper.fills = [];
  wrapper.resize(instance.width, instance.height);
  wrapper.appendChild(instance);
  frame.appendChild(wrapper);
}
```

---

### Step 5: スクリーンショットの取得と保存

生成した各フレームのnode IDを確認してからスクリーンショットを取得する。
```
get_screenshot(frameNodeId)
```

保存先：
- `images/{component-name}-size-light.png`
- `images/{component-name}-state-light.png`
- `images/{component-name}-shape-light.png`

**重要：** `get_screenshot` の戻り値を直接使わず、必ずファイルとして保存してからパスを参照する。

---

### Step 6: MDXへの挿入

出力先MDXの `## バリアント` セクション内、sizeテーブルの直後に以下の順で挿入する：
```md
## バリアント

### Size

![Button Size](/images/button-size-light.png)

| サイズ | 用途 |
|---|---|
| sm | （説明） |
| md | （説明） |
| lg | （説明） |
| xl | （説明） |

### State

![Button State](/images/button-state-light.png)

| ステート | 説明 |
|---|---|
| Default | 通常状態 |
| Hover | カーソルが乗っている状態 |
| Pressed | クリック・タップ中の状態 |
| Focused | キーボードフォーカス状態 |
| Disabled | 操作不可状態。`opacity: 0.4` を適用 |

### Shape

![Button Shape](/images/button-shape-light.png)

| 形状 | 説明 |
|---|---|
| Default | 標準の角丸 |
| Full radius | ピル型。角丸を最大にした形状 |

### その他のプロパティ

| プロパティ | 説明 |
|---|---|
| hasLeftIcon / hasRightIcon | アイコンの表示・非表示を切り替える |
| isPending | 処理中状態。ローディングインジケーターを表示する |
```

`figma-frame` コメントは各画像の直前に追記する。

---

## フォールバック

| 状況 | 対処 |
|---|---|
| プロパティが存在しない | そのセクションをスキップする |
| フレーム生成に失敗 | `<!-- TODO: {セクション名}フレームを手動で作成後に更新 -->` を挿入して続行 |
| スクリーンショット取得に失敗 | `<!-- figma-frame: 取得失敗 - node IDを手動で記入 -->` を挿入して続行 |
