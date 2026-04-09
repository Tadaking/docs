# スキル：Anatomy フレーム生成

## 役割

Figmaのテンプレートコンポーネントを複製してAnatomyフレームを生成し、
スクリーンショットをMDXのAnatomyセクションに挿入する。

`generate-component-doc.md` からサブスキルとして呼び出されるほか、単独でも使用できる。

---

## インプット

| 項目 | 必須 | 説明 |
|---|---|---|
| 対象コンポーネントの Figma URL または node ID | ✅ | AnatomyフレームのプレースホルダーにコピーするコンポーネントのノードID |
| 出力先MDXファイルパス | ✅ | Anatomyセクションの挿入先 |

---

## 実行手順

### Step 1: コンポーネント構造の取得
```
figma_get_component_for_development(nodeId)
```

以下を取得する：
- **実際に存在する子レイヤーのみ**をリストアップする
- 非表示レイヤー・マスクレイヤーは除外する
- レイヤーとして存在しない要素（border、shadow等の見た目上の効果）は含めない
- 各レイヤーの **`name`**（Figma上のレイヤー名そのまま）と **`type`**（INSTANCE / TEXT / FRAME / VECTOR 等）を記録する
- `componentPropertyReferences`（`visible` / `mainComponent` 等）も記録する。表示制御プロパティの参照先を Notes 列で説明するため

### Step 1.5: Annotation の取得

```
figma_get_annotations(nodeId, include_children: true, depth: 5)
```

コンポーネント本体および子レイヤーに付与された Annotation を取得する。

- 各レイヤー ID をキーに Annotation 本文をマップしておく
- Annotation がゼロでも問題ない。後続ステップは AI フォールバックに倒れる
- Annotation がある場合は、対応するレイヤーの Notes 列に **そのまま反映**する（AI の校正・要約は行わない）

### Step 2: 要素の分類とテーブルの準備

**Element name 列の決定ルール（優先順位）**

1. **Figma の実レイヤー名**を最優先で採用する。そのまま `` `bg` `` のようにバッククォートで囲んで記載する
2. Annotation に名前指定がある場合のみ、それで上書きする
3. 上記いずれも該当しない場合（=ほぼ起こらない）のみ AI が推測し、`<!-- TODO: 名前を確認 -->` を添える

**絶対にやってはいけないこと：**
- レイヤー名を「親しみやすい名前」に翻訳する（例：`bg` → "Background"、`Label Text` → "Label"、`Icon Container` → "Left Icon"）
- Figma に存在しない名前を作る
- 同名レイヤーが複数ある場合（例：左右の `Icon Container`）に、無理に区別名を付けて差し替える。同じ Element name のまま Notes 列で違いを説明する

**Type 列の決定ルール**

| Figma type | Type 列の値 |
|---|---|
| INSTANCE | Sub-component |
| TEXT | Text |
| FRAME / GROUP（子が複数あり、レイアウト目的） | Container |
| FRAME / GROUP（子が1つで純粋に装飾目的） | Structural |
| RECTANGLE / VECTOR / ELLIPSE 等 | Structural |

**Notes 列の決定ルール（優先順位）**

1. **Annotation 本文**があればそれをそのまま採用する
2. なければ AI が以下の文脈から起案する：
   - レイヤー名
   - type
   - `componentPropertyReferences.visible`（boolean プロパティで表示制御されている場合は「`{propName}` プロパティで表示制御」と書く）
   - `componentPropertyReferences.mainComponent` または `componentPropertyReferences.characters`（インスタンススワップやテキストプロパティ）
   - 親コンポーネントの種類（Button なのか Icon Button なのか）

**AI 起案 Notes の書き方ガイド：**
- 1〜2 文以内、簡潔に
- 見た目（色・サイズ）ではなく**役割**を書く
- CLAUDE.md の文体ルール（伴走者トーン、命令形を避ける）に従う
- レイヤー名がデフォルト名（`Frame 23`、`Rectangle 4` 等）の場合は AI 起案を諦め、`<!-- TODO: 役割説明を追加 -->` を入れる

### Step 3: テンプレートの複製

Figma Console MCP を使い、以下のテンプレートコンポーネントを複製する：

- テンプレートURL: https://www.figma.com/design/Pvlx7A6oDLlnPF8MQYFDcq/Web-Components?node-id=5061-14376
- 複製先：対象コンポーネントと同じページの隣

複製後に以下のテキストを書き換える：
- `{component-name-anatomy-or-property}` → `{ComponentName} Anatomy`
- `{brief-component-description}` → Step 1で取得したコンポーネントのdescription（なければ空欄）
- `{section-name}` → `Elements`
- `{optional-description}` → 補足説明がある場合は内容を書き換える。不要な場合はレイヤーを削除する

### Step 4: コンポーネントの配置

グレーのプレースホルダーエリアに対象コンポーネントのインスタンスを配置する。

- defaultのバリアントを使用する
- プレースホルダーエリアの中央に配置する

### Step 4.5: ドキュメント用アイコンのスワップ

配置したインスタンス内に `Icon Container` のネストされたインスタンスがある場合、デフォルトの `_iconSlot`（点線ボックスのプレースホルダー）が表示されてしまう。これを `_swap_icons`（ドキュメント専用に作られたアイコンセット）にスワップする。

**スワップ先：**

| 位置 | nodeId | 中身 |
|---|---|---|
| `position=left` | `5291:19589` | ★ 星 |
| `position=right` | `5291:19602` | › chevron-right |

**判定ロジック：**

- Icon Container の `componentPropertyReferences.visible` を確認
- `hasLeftIcon...` で始まる → 左（star）にスワップ
- `hasRightIcon...` で始まる → 右（chevron）にスワップ
- 上記いずれでもない（Icon Button のような単一スロット） → 左（star）にフォールバック

**コードパターン：**

```javascript
async function swapDocIcons(rootInstance) {
  const LEFT_ICON_ID = '5291:19589';   // _swap_icons / position=left (star)
  const RIGHT_ICON_ID = '5291:19602';  // _swap_icons / position=right (chevron)

  // ネストされた Icon Container instance を全部拾う
  const iconContainers = rootInstance.findAll(
    n => n.type === 'INSTANCE' && n.name === 'Icon Container'
  );

  for (const ic of iconContainers) {
    // visible reference から左右を判定
    const visibleRef = ic.componentPropertyReferences && ic.componentPropertyReferences.visible;
    let targetIconId = LEFT_ICON_ID; // フォールバック（単一スロットも左を採用）
    if (visibleRef && visibleRef.startsWith('hasRightIcon')) {
      targetIconId = RIGHT_ICON_ID;
    }

    // icon プロパティのキーを動的に発見（`icon#xxx:y` のサフィックスは Icon Container の定義による）
    const iconPropKey = Object.keys(ic.componentProperties).find(k => /^icon(#|$)/.test(k));
    if (iconPropKey) {
      ic.setProperties({ [iconPropKey]: targetIconId });
    }
  }
}

// 使用例：Step 4 で配置した instance に対して呼ぶ
await swapDocIcons(placedInstance);
```

**重要：** このスワップは Icon Container が**表示されている**ときのみ視覚的に効果がある。`hasLeftIcon = false` で非表示の場合はそのまま隠れたままで、スワップは無害な no-op になる。よって「アイコンが見えるべきところには `_swap_icons` の実アイコン、見えるべきでないところはそのまま非表示」という挙動が自動で得られる。

### Step 5: 番号バッジと引き出し線の配置

各要素のbboxを使い、バッジを上下左右の最適な方向に振り分けて配置する。

#### 5-1: 配置方向の決定

コンポーネント全体のbboxを基準に、各要素の位置から配置方向を決める：

- 要素がコンポーネントの**左端付近** → バッジを左に配置（線は水平・左方向）
- 要素がコンポーネントの**右端付近** → バッジを右に配置（線は水平・右方向）
- 要素がコンポーネントの**上端付近** → バッジを上に配置（線は垂直・上方向）
- 要素がコンポーネントの**下端付近** → バッジを下に配置（線は垂直・下方向）
- 中央付近の要素は隣接する要素と被らない方向を選ぶ

同じ方向に複数のバッジが集中する場合は、バッジ同士が重ならないよう等間隔でずらす。

#### 5-2: バッジの配置

テンプレートの `#` バッジを要素の数だけ複製し、決定した方向にコンポーネントから **40px** 離して配置する。
バッジの番号テキストを `1`, `2`, `3`... に書き換える。

バッジの色: `#E7006C`

#### 5-3: 引き出し線の描画

`figma_execute` を使い、各バッジから対応する**ターゲット要素のエッジ**まで直線1本を描画する。折れ線・L字・曲線は使わない。

##### ターゲット要素の決め方

「バッジ → ターゲット要素」のマッピングは、Step 1 で取得したレイヤー情報をベースに作る：

| バッジが指す要素 | ターゲットノード |
|---|---|
| Background（`bg` 等） | `bg` レイヤー本体（コンポーネント全体と同サイズ） |
| 内部要素（`Icon Container`、`Label Text` 等） | その内部レイヤー本体 |

**重要**：badge 2 が `Left Icon Container` を指す場合、ターゲットはボタン全体ではなく **`Icon Container` のレイヤー**。線はそのレイヤーのエッジまで届かせる。

##### 座標系の扱い（ハマりどころ）

- ターゲット要素・バッジの位置取得は **必ず `absoluteBoundingBox`** を使う（絶対座標）
- `figma.createLine()` で作った line の `line.x`、`line.y` は **親フレーム基準の相対座標**
- 絶対座標 → 相対座標の変換は `親フレームの absoluteBoundingBox.x/y を引く`
- ノード取得は **`figma.getNodeByIdAsync`** を使う。`getNodeById`（同期版）は dynamic-page モードでエラーになる

##### ラインの始点・終点ルール（厳密に書く）

水平線の場合（badge が target の左 or 右にある）：
- y は **target の縦中心**（`tAbs.y + tAbs.height / 2`）
- 左方向（badge が左）：x の範囲は `[badge右端, target左端]`
- 右方向（badge が右）：x の範囲は `[target右端, badge左端]`

垂直線の場合（badge が target の上 or 下にある）：
- x は **target の横中心**（`tAbs.x + tAbs.width / 2`）
- 上方向（badge が上）：y の範囲は `[badge下端, target上端]`
- 下方向（badge が下）：y の範囲は `[target下端, badge上端]`

ライン両端は **target エッジに正確に接する**。視覚的な breathing space は入れない。

##### 実装パターン（テスト済み）

```javascript
async function drawLeaderLine({ parentId, badgeId, targetId, direction }) {
  const parent = await figma.getNodeByIdAsync(parentId);
  const badge = await figma.getNodeByIdAsync(badgeId);
  const target = await figma.getNodeByIdAsync(targetId);

  const pAbs = parent.absoluteBoundingBox;
  const bAbs = badge.absoluteBoundingBox;
  const tAbs = target.absoluteBoundingBox;

  let absX, absY, lineLength, isVertical;

  if (direction === 'left') {
    // badge は target の左、線は水平
    absX = bAbs.x + bAbs.width;          // line bbox 左端 = badge 右端
    absY = tAbs.y + tAbs.height / 2;     // target 縦中心
    lineLength = tAbs.x - absX;          // target 左端まで
    isVertical = false;
  } else if (direction === 'right') {
    // badge は target の右、線は水平
    absX = tAbs.x + tAbs.width;          // line bbox 左端 = target 右端
    absY = tAbs.y + tAbs.height / 2;
    lineLength = bAbs.x - absX;          // badge 左端まで
    isVertical = false;
  } else if (direction === 'top') {
    // badge は target の上、線は垂直
    absX = tAbs.x + tAbs.width / 2;      // target 横中心
    absY = bAbs.y + bAbs.height;         // line bbox 上端 = badge 下端
    lineLength = tAbs.y - absY;          // target 上端まで
    isVertical = true;
  } else if (direction === 'bottom') {
    // badge は target の下、線は垂直
    absX = tAbs.x + tAbs.width / 2;
    absY = tAbs.y + tAbs.height;         // line bbox 上端 = target 下端
    lineLength = bAbs.y - absY;          // badge 上端まで
    isVertical = true;
  }

  const line = figma.createLine();
  // 絶対 → 親相対
  line.x = absX - pAbs.x;
  line.y = absY - pAbs.y;
  line.resize(lineLength, 0);

  line.strokes = [{ type: 'SOLID', color: { r: 0.906, g: 0, b: 0.424 } }]; // #E7006C
  line.strokeWeight = 1;
  line.opacity = 0.7;
  parent.appendChild(line);

  if (isVertical) {
    // Figma の LINE はデフォルト水平。-90 度回転で垂直化する。
    // 重要：rotation = -90 を設定しても line.x / line.y は変化せず、
    //   bbox 左上は (absX, absY) のまま、線がそこから DOWN 方向に lineLength 伸びる。
    //   よって追加の y 補正は不要。
    line.rotation = -90;
  }

  return { id: line.id, abs: line.absoluteBoundingBox };
}
```

##### 検証ステップ（必須）

線を引いた後、各 line の `absoluteBoundingBox` を取得し、以下を確認する：

- **水平線**：`absoluteBoundingBox.y` が `target.absoluteBoundingBox.y` の縦範囲内にあること、かつ `x` または `x + width` のいずれかが target の対応エッジに一致していること
- **垂直線**：`absoluteBoundingBox.x` が target の横範囲内にあり、`y` または `y + height` が target の対応エッジに一致していること

ズレている場合は：
1. 絶対座標と相対座標が混在していないか確認（`absoluteBoundingBox` と `node.x / node.y` の取り違え）
2. ターゲットノードが期待通りのレイヤーか（Background なのに Button Container を渡している等）を確認
3. 垂直線で rotation を `+90` ではなく `-90` にしているか確認（+90 だと線が上方向に出てしまう）

3 回試して直らない場合は、TODO コメントを残して手動修正に倒す。

### Step 6: 画像の書き出しと保存

Figma Console MCP の `figma_get_component_image` を使って生成したAnatomyフレームを書き出す。

- 対象：生成した `{ComponentName}/Anatomy` フレームのnode ID
- 保存先：`images/{component-name}-anatomy-light.png`
- `get_screenshot` / `figma_execute` での base64 エクスポートは使わない

### Step 7: MDXへの挿入

出力先MDXファイルの `## Anatomy` セクションを以下の形式で更新する：
```md
{/* figma-frame: FILE_KEY/NODE_ID | Anatomy */}

![{ComponentName} Anatomy](/images/{component-name}-anatomy-light.png)

| # | Type | Element name | Notes |
|---|---|---|---|
| 1 | Structural | `bg` | {Annotation か AI 起案の役割説明} |
| 2 | Sub-component | `Icon Container` | {同上} |
| 3 | Text | `Label Text` | {同上} |
```

**列の埋め方：**
- `<!-- figma-frame -->` コメントはnode IDの記録用に画像の直前に追記する
- **Element name** はバッククォートで囲んだ Figma レイヤー名そのまま
- **Type** は `Sub-component` / `Text` / `Container` / `Structural`
- **Notes** は Annotation 本文 → なければ AI 起案（Step 2 のルール参照）

**番号と Figma 上のバッジの対応：**
- テーブルの番号（1, 2, 3...）は Figma `_Anatomy` フレーム内のバッジ番号と一致させる
- 同じレイヤー名が複数登場することがある（例：左右の `Icon Container`）。その場合も Element name は同じまま記載し、Notes 列の説明（`hasLeftIcon` / `hasRightIcon` プロパティ参照など）で違いを明確にする

---

## フォールバック

| 状況 | 対処 |
|---|---|
| テンプレートの複製に失敗 | `<!-- TODO: Anatomyフレームを手動で作成後に更新 -->` を挿入して続行 |
| コンポーネントの配置に失敗 | フレームだけ生成してコンポーネントのnode IDをコメントに残す |
| スクリーンショットがずれる | node IDを再確認して再取得。それでも失敗する場合は `<!-- figma-frame: 取得失敗 - node IDを手動で記入 -->` を挿入 |
| 子レイヤーが0件 | `figma_get_component_for_development` を再実行し、それでも取得できない場合はTODOコメントを挿入して続行 |
