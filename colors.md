#  Colors

> 色は階層を確立し、ブランディングを強化し、調和を築きます。

---

## 1. Overview

### Quicklook

プロダクト全体で、見やすく・使いやすく・一貫した配色の実現を目指しています。

- カラーのトークンは「Theme → Lightness-mode → Component」の3つの階層で管理し、デザインの一貫性と、あとから増やしやすい構造を保つ
- Lightness-mode のトークンでは、「surface」「on-surface」「border」など、役割ごとにカテゴリを分けている
- 色の設計は APCA によるコントラスト評価をもとに行い、アクセシビリティを確保する

### Design Philosophy

- **OKLCHカラーモデル**に基づいて設計。oklchとは明度や彩度の変化が知覚的に均一になるように設計されたカラーモデル
- **18段階の色階調**により、1つのスケールでライトモードとダークモードの両方に対応
- **APCAコントラスト指標**で評価し、アクセシビリティを最優先

### トークンの階層（3層構造）

| 層                      | 役割                                                                    |
| ---------------------- | --------------------------------------------------------------------- |
| **Theme**              | 各ブランドごとに基本色と生成ルールを定義する基盤層                                             |
| **Lightness-Mode**     | 背景やテキストなど、UIに直接関わるセマンティック層のトークン                                       |
| **Component-Specific** | ⚠️デザイナー向けのみ（Figma Variables）。ボタンなど実際のUIコンポーネントに、どの色をどの役割で使うかをマッピングする層 |

### Theme (Primitive Tokens) の概要

各ブランドで共通して使う基礎色セットを定義するPrimitive層。ブランドイメージに直結する色や、OKLCHに基づく色階調を扱い、プロダクト全体の"らしさ"を支える土台。この層ではテキストや背景など具体的な用途はまだ固定せず、「どんな色を使ってよいか」の範囲と性質にフォーカスする。

**コミュニケーションデザインを行う場合は、この Theme トークンを直接使用できる。**

#### Brand Color スケール

このカラースケールに設定された色は、Lightness-mode Tokens における Primary Action カラーとして適用される。ただし、ブランドカラーが必ずしもUIのプライマリーカラーになるとは限らない。そのため、各ブランドのUIごとに、どの色をPrimary Colorとして扱うかをあらかじめ定義しておく必要がある。

#### ブランドテーマ設計の考え方

- Lightness-modeの参照ルール（どのLightnessトークンが、どのthemeトークンを使うか）を前提に設計する
- 既存のLightness-modeの構造を崩さずに、ブランドらしさを表現できる色やスタイルをあて込む
- 「好きな色を当てる」のではなく、「決められたthemeトークンに、自分たちのブランドの色をどう置き換えるか」という視点で考える

#### スティーヴンスのべき法則

スティーブンスのベキ法則を参考に、ガンマ補正（Gamma ≈ 1.45）を適用した非線形カーブを採用。各カラースケールは0〜100の18段階で設計されており、OKLCH色空間のL値（Luminance）を基準に非線形で配置。

非線形スケールのメリット：

- 各ステップ間の「見た目の差」がおおよそ均一になる
- ダーク／ライトテーマ間で、自然なトーンバランスを保ちやすい
- 階調が滑らかなので、背景レイヤーやホバー／アクティブ状態の設計がしやすい

### Lightness-Mode (Semantic Tokens) の概要

実際のプロダクトUI要素に直接適用される、意味をもった色を定義する層。配色指定はこのセマンティックトークンを通じて行う。

- ✅ 使うべき場所が明確になる
- ✅ アクセシビリティが保証される
- ✅ ダークモード対応が自動化される

### Color role

| カテゴリ       | トークン                                                    | 説明                                            |
| ---------- | ------------------------------------------------------- | --------------------------------------------- |
| Surface系   | surface                                                 | 構造的な背景レイヤー                                    |
| Container系 | surface-container, accent-container, feedback-container | ボタンやinputなどテキストやアイコンやUIなどを含む要素の背景色            |
| Border系    | border, accent-border, feedback-border                  | コンテナの周囲の枠線、罫（ストローク）、またはディバイダー                 |
| on系        | on-surface, on-accent, on-feedback, accent, feedback    | surface, container系の上に配置されるコンテンツ（テキスト・アイコンなど） |
| Shadow系    | shadow                                                  | 視覚的にエレベーションを表現するドロップシャドウ                      |

### 色の強弱

各カテゴリのトークンには、視覚的な強弱を表現するための階層が定義されている。階層は相対的な関係を表すものであり、特定の状態に固定して紐づけるものではない。

**ライトモードでは濃く、ダークモードでは明るくすることで、視覚的な強調を行う。**

強弱の階層: `whisper（最も薄い）→ subtle → soft → medium → bold → heavy（最も濃い）`

#### 例: surface-container

| トークン                            | 参照先                        |
| ------------------------------- | -------------------------- |
| color.surface-container.whisper | `{color.neutral-alpha.5}`  |
| color.surface-container.subtle  | `{color.neutral-alpha.10}` |
| color.surface-container.medium  | `{color.neutral-alpha.15}` |
| color.surface-container.bold    | `{color.neutral-alpha.20}` |
| color.surface-container.heavy   | `{color.neutral-alpha.25}` |

#### 例: accent-container

| トークン                                   | 用途例                       | 参照先                         |
| -------------------------------------- | ------------------------- | --------------------------- |
| color.accent-container.primary.ghost   |                           | `{color.accent.primary.5}`  |
| color.accent-container.primary.whisper | セカンダリーアクションなど             | `{color.accent.primary.15}` |
| color.accent-container.primary.subtle  |                           | `{color.accent.primary.20}` |
| color.accent-container.primary.soft    |                           | `{color.accent.primary.25}` |
| color.accent-container.primary.medium  | Primary ActionやSelectedなど | `{color.accent.primary.50}` |
| color.accent-container.primary.bold    |                           | `{color.accent.primary.60}` |
| color.accent-container.primary.heavy   |                           | `{color.accent.primary.70}` |

### 特殊な修飾子

| 修飾子          | 説明                                                                     |
| ------------ | ---------------------------------------------------------------------- |
| .fixed       | テーマモードに関わらず固定の色。ライト/ダークモードが切り替わっても常に同じ色を保つ                             |
| .inverse     | 反転色。ツールチップやトーストなど、周囲と明確に区別したい要素に使用                                     |
| .alternative | 代替色。同じ役割を保ちながら視覚的な変化を付けたい場合や、階層構造を表現したい場合に使用                           |
| .primary     | プライマリ/メインバリエーション。accent-containerやaccent-borderなど、複数バリエーションを持つカテゴリの代表色 |

💡 これらの修飾子は組み合わせて使える（例：surface-raised-inverse-fixed）。

### インタラクションの状態

#### 1. 相対的な濃淡変化

- ライトモードでは濃くすることで強調
- ダークモードでは薄く（明るく）することで強調
- 基準色に応じて、適切な方向に1-2段階変化させる

例1: defaultを基準とする場合

- Default: surface-container-default
- Hover: surface-container-whisper（1段階薄く）
- Active: surface-container-subtle（さらに濃く）

例2: whisperを基準とする場合

- Default: surface-container-whisper
- Hover: surface-container-subtle（1段階濃く）
- Active: surface-container-medium（さらに濃く）

#### 2. Disabled状態 = 40% opacity

無効状態では、カラートークンは変更せず、opacity: 0.4（40%）を一律で適用する。

#### 3. Focus Ring = 固定色

キーボードフォーカス時のフォーカスリングは、画面や使う場所に関係なく、いつも同じ色を使う。

### アクセシビリティ

APCA（Advanced Perceptual Contrast Algorithm）を採用し、コントラスト評価でブロンズレベルを目指す。

- ✅ UIテキストにおいては、Lc値は60以上を目標
- ✅ 段落などの本文には75以上を目標
- ❌ PlaceholderやDisabled、アイコン以外のUIコンテンツにはLc60以上を目安

---

## 2. Usage

### 実装ステータス

|---|---|---|---| | | Web | iOS | Android | | Light Mode | ✅ Ready | To do | To do | | Dark Mode | To do | To do | To do |

### 原則

すべてのUIコンポーネントは、**原則的にLightness-Modeトークンから選択する必要がある**。デザイナーや開発者は、まずこのLightness-Modeトークンの中から最適な色を探し、その範囲で表現を工夫すること。

Lightness-Modeトークンを使うメリット：

1. ダークモード対応が自動化される
2. アクセシビリティが保証される
3. 一貫性のある体験を提供できる
4. テーマ拡張・調整時の影響範囲を把握しやすくなる

💡 新しい色が必要だと感じたら、既存のLightness-Modeトークンで代替できないかを検討すること。

### 例外：Themeトークンを使える条件

色そのものが特定の意味を持たず、他の色に置き換えても体験が変わらない場合。

#### ✅ Themeトークンを使える例

|---|---| | ユースケース | 理由 | | プロジェクトアイコンの背景色 | 色は識別のためで、緑でも青でも機能は同じ | | カラーピッカーの色見本 | 色そのものを選ぶ機能 | | 付箋・タグのカラーバリエーション | ユーザーが好きな色で分類する | | データ可視化の系列色 | 色は区別のためで、特定の意味はない |

#### ❌ Themeトークンを使ってはいけない例

|---|---|---| | ユースケース | 理由 | 正しいトークン | | エラーメッセージの背景 | 赤色が「エラー」という意味を持つ | feedback-container.assertive | | 成功通知 | 緑色が「成功」という意味を持つ | feedback-container.positive | | 警告バナー | 黄色が「注意」という意味を持つ | feedback-container.notice | | プライマリーボタン | ブランド色がアクションの重要度を示す | accent-container.primary |

⚠️ Themeトークンを使用する場合は、ダークモード対応を自分で実装する必要がある。

💡 判断に迷ったら：

1. 「この色を変えてもユーザー体験は変わらない？」を自問する
2. それでも不明な場合は #design で @garage-crew に相談
3. Lightness-ModeのAccentカテゴリへの追加提案も検討

### Surface

Surfaceとは、UI要素が配置されるキャンバスのこと。サーフェスカラーは、アプリの視覚的な重なり順を表現する。

対象要素：アプリケーションの背景、パネル、モーダルウインドウ、ドッキングコンテナ、ポップオーバー

#### バリエーション

**1. Base** — アプリケーションの最下層となる基本背景

- default: 白地の背景
- alternative: グレー地の背景（セクション区切りや交互背景など）
- 用途例: メインコンテンツエリア、アプリケーション全体のベース、モバイルアプリのビューポート背景

**2. Raised** — コンテンツが基本サーフェスよりも浮き上がっていることを表現

- level1: 各Surfaceに対する相対的なエレベーション
- level2: 各Surfaceに対する相対的なエレベーション
- inverse: 暗い背景
- inverse-fixed: ダークモード切り替え時も固定される暗い背景
- 用途例: ナビゲーションパネル、サイドバー

**3. Shunken** — コンテンツがページ内に「沈み込んでいる」印象

- 用途例: コードエディタ背景、インラインフレーム

**4. Scrim** — 背後のコンテンツを視覚的に減衰させ、前面のコンテンツに注目を集める

- default: モーダルダイアログの背景やフルスクリーンローディング画面
- fixed: 動画ビューモードなど

**5. Overlay** — 最上層のサーフェス

- 用途例: モーダルダイアログのコンテンツ背景、フルスクリーン背景、ポップオーバーコンテンツ背景

### Surface-Container

Surface上に配置され、テキストやアイコンなどの他のUI要素を含むニュートラルなコンテキストを持った要素専用。カード、ボタン、タブセット、インプット要素。

### On-Surface

SurfaceまたはSurface-container上に表示されるテキストやアイコンに使用。コンテンツが色のコントラスト要件を満たすことが保証される。

### Borders

コンテナの周囲、ストローク、または区切り線に使用。

### Feedback

フィードバックカラーは、ユーザーにアクションやイベントの状態に関するセマンティックな情報を視覚的に伝えるために使用。

|---|---| | トークン | 用途 | | feedback | 直接surface上に配置されるラベルやアイコン向け | | feedback-container | 背景色。フィードバック内容をまとめるコンテナ | | feedback-border | 枠線による状態の強調 | | on-feedback | 背景（feedback-container）の上に載るテキスト・アイコンの色 |

### Semantic colorsの選択ガイド

|---|---|---|---|---|---| | | Action | Message | Connectivity | Status | Priority | | Neutral | Default | Neutral | Unavailable | Default, To do, Draft, Not started, Ended | Trivial | | Primary (Brand) | Primary, Confirm, Save | Positive | Success, Available | Approved, Complete | Minor | | Assertive | Remove | Error | Busy | Removed or Failed, Rejected | Critical | | Notice | | Warning | | Notice, Pending, Needs approval | Medium to blocker | | Info | | Information | | In progress | |

### Accent

アクセントカラーは、ユーザーインターフェース全体でブランドのアクセントカラーを表現したり、アクションへの注意を引くために使用。ボタンやリンクなどのページ上のアクションを強調する。

|---|---| | トークン | 用途 | | accent.#type | surface, surface-containerに直接配置されるテキストやアイコンなどのカラー | | accent-container.#type | 背景色。フィードバック内容をまとめるコンテナ | | accent-border.#type | 枠線による状態の強調 | | on-accent.#type | 背景の上に載るテキスト・アイコンの色。コントラストを確保した前景色 |

---

## 3. Theme Tokens（プリミティブ値）

> データソース: Figma Variables — コレクション "Theme"、モード "newmo"

Theme Tokensは生のカラーパレットを定義する。これらの値は**ライトモード・ダークモードで共通**であり、モードによって変化するのはLightness-Mode Tokensのみ。

### accent/amber

| ステップ | HEX       |
| ---- | --------- |
| 0    | `#FFFFFF` |
| 5    | `#FFF9E6` |
| 10   | `#FFF4D2` |
| 15   | `#FFEEBA` |
| 20   | `#FFE595` |
| 25   | `#FFD547` |
| 30   | `#E9BE05` |
| 40   | `#C6A002` |
| 50   | `#AA8A04` |
| 60   | `#8D7102` |
| 70   | `#7D6400` |
| 80   | `#665200` |
| 85   | `#4F3F01` |
| 90   | `#392C00` |
| 95   | `#261D00` |
| 98   | `#1A1300` |
| 99   | `#0F0A00` |
| 100  | `#000000` |

### accent/beige

| ステップ | HEX       |
| ---- | --------- |
| 0    | `#FFFFFF` |
| 5    | `#FEF9EF` |
| 10   | `#FCF3E1` |
| 15   | `#FCEDCD` |
| 20   | `#F9E4B5` |
| 25   | `#F1D794` |
| 30   | `#DBC070` |
| 40   | `#BCA34D` |
| 50   | `#A2882F` |
| 60   | `#8B7210` |
| 70   | `#7B6510` |
| 80   | `#645316` |
| 85   | `#4F3F12` |
| 90   | `#372D07` |
| 95   | `#251D02` |
| 98   | `#191402` |
| 99   | `#0F0A02` |
| 100  | `#000000` |

### accent/copper

| ステップ | HEX       |
| ---- | --------- |
| 0    | `#FFFFFF` |
| 5    | `#FDF8F5` |
| 10   | `#FCF2EC` |
| 15   | `#FDEADF` |
| 20   | `#FCE0D1` |
| 25   | `#FCCFB2` |
| 30   | `#F3B288` |
| 40   | `#E08E58` |
| 50   | `#CA7538` |
| 60   | `#B05A11` |
| 70   | `#9D4F0C` |
| 80   | `#823F05` |
| 85   | `#643106` |
| 90   | `#4A2100` |
| 95   | `#311501` |
| 98   | `#230D01` |
| 99   | `#150600` |
| 100  | `#000000` |

### accent/cyan

| ステップ | HEX       |
| ---- | --------- |
| 0    | `#FFFFFF` |
| 5    | `#F3FBFE` |
| 10   | `#E7F7FF` |
| 15   | `#DDF2FE` |
| 20   | `#CFEBFC` |
| 25   | `#AEE1FF` |
| 30   | `#71CFFF` |
| 40   | `#16B2F0` |
| 50   | `#129ACF` |
| 60   | `#037FAC` |
| 70   | `#127098` |
| 80   | `#085B7E` |
| 85   | `#064762` |
| 90   | `#053246` |
| 95   | `#002131` |
| 98   | `#001622` |
| 99   | `#010D13` |
| 100  | `#000000` |

### accent/green

| ステップ | HEX       |
| ---- | --------- |
| 0    | `#FFFFFF` |
| 5    | `#F1FDF4` |
| 10   | `#E7F9EC` |
| 15   | `#D8F8E2` |
| 20   | `#C2F5D2` |
| 25   | `#9BEFB9` |
| 30   | `#6EDD9A` |
| 40   | `#33C176` |
| 50   | `#00A55E` |
| 60   | `#018B4F` |
| 70   | `#007C45` |
| 80   | `#006538` |
| 85   | `#004F2A` |
| 90   | `#02381D` |
| 95   | `#002511` |
| 98   | `#001A0A` |
| 99   | `#010F05` |
| 100  | `#000000` |

### accent/indigo

| ステップ | HEX       |
| ---- | --------- |
| 0    | `#FFFFFF` |
| 5    | `#F8F9FF` |
| 10   | `#F0F4FF` |
| 15   | `#E8EEFE` |
| 20   | `#DFE5FB` |
| 25   | `#CCD8FF` |
| 30   | `#AEBFFF` |
| 40   | `#879DFF` |
| 50   | `#6A7FFF` |
| 60   | `#4F5BF8` |
| 70   | `#433DFE` |
| 80   | `#3624DD` |
| 85   | `#2919B1` |
| 90   | `#1C0B85` |
| 95   | `#110061` |
| 98   | `#0B0048` |
| 99   | `#05022D` |
| 100  | `#000000` |

### accent/orange

| ステップ | HEX       |
| ---- | --------- |
| 0    | `#FFFFFF` |
| 5    | `#FEF8F3` |
| 10   | `#FEF1E6` |
| 15   | `#FEEADC` |
| 20   | `#FCE0CC` |
| 25   | `#FFCEA7` |
| 30   | `#FFAE67` |
| 40   | `#EF8604` |
| 50   | `#CE7402` |
| 60   | `#AB5E02` |
| 70   | `#985401` |
| 80   | `#7B4508` |
| 85   | `#613404` |
| 90   | `#442503` |
| 95   | `#2F1600` |
| 98   | `#220E00` |
| 99   | `#140700` |
| 100  | `#000000` |

### accent/pink

| ステップ | HEX       |
| ---- | --------- |
| 0    | `#FFFFFF` |
| 5    | `#FEF7F7` |
| 10   | `#FFF0F0` |
| 15   | `#FEE8E8` |
| 20   | `#FDDDDD` |
| 25   | `#FFCACB` |
| 30   | `#FFA7AA` |
| 40   | `#FF6F7C` |
| 50   | `#FA3356` |
| 60   | `#D8013F` |
| 70   | `#C10338` |
| 80   | `#9E062D` |
| 85   | `#7E0020` |
| 90   | `#5B0016` |
| 95   | `#3F000C` |
| 98   | `#2E0007` |
| 99   | `#1D0003` |
| 100  | `#000000` |

### accent/primary

| ステップ | HEX       |
| ---- | --------- |
| 0    | `#FFFFFF` |
| 5    | `#F3FCF3` |
| 10   | `#EAF9EA` |
| 15   | `#DBF8DB` |
| 20   | `#CAF4C9` |
| 25   | `#A6EEA6` |
| 30   | `#7BDD7E` |
| 40   | `#43C24A` |
| 50   | `#00A724` |
| 60   | `#028D1D` |
| 70   | `#027E19` |
| 80   | `#076615` |
| 85   | `#01500D` |
| 90   | `#003906` |
| 95   | `#002603` |
| 98   | `#011A03` |
| 99   | `#020F02` |
| 100  | `#000000` |

### accent/purple

| ステップ | HEX       |
| ---- | --------- |
| 0    | `#FFFFFF` |
| 5    | `#F8F8FE` |
| 10   | `#F2F2FF` |
| 15   | `#EDECFE` |
| 20   | `#E4E3FD` |
| 25   | `#D6D4FF` |
| 30   | `#BFB9FF` |
| 40   | `#A292FF` |
| 50   | `#8C71FF` |
| 60   | `#7649F6` |
| 70   | `#6D22F7` |
| 80   | `#591BCA` |
| 85   | `#4511A1` |
| 90   | `#310977` |
| 95   | `#200056` |
| 98   | `#150040` |
| 99   | `#0C002B` |
| 100  | `#000000` |

### accent/red

| ステップ | HEX       |
| ---- | --------- |
| 0    | `#FFFFFF` |
| 5    | `#FEF7F6` |
| 10   | `#FFF0EF` |
| 15   | `#FFE9E4` |
| 20   | `#FFDDD8` |
| 25   | `#FFCBC1` |
| 30   | `#FFA99A` |
| 40   | `#FF735F` |
| 50   | `#F43828` |
| 60   | `#DA0900` |
| 70   | `#BE1E12` |
| 80   | `#9E0E05` |
| 85   | `#7E0601` |
| 90   | `#5A0703` |
| 95   | `#400000` |
| 98   | `#2E0100` |
| 99   | `#1C0100` |
| 100  | `#000000` |

### accent/teal

| ステップ | HEX       |
| ---- | --------- |
| 0    | `#FFFFFF` |
| 5    | `#EFFDFD` |
| 10   | `#E5F8F9` |
| 15   | `#D9F5F6` |
| 20   | `#C0F1F3` |
| 25   | `#8EECF0` |
| 30   | `#3FDBE2` |
| 40   | `#14BBC2` |
| 50   | `#1AA1A7` |
| 60   | `#01858B` |
| 70   | `#13767A` |
| 80   | `#026065` |
| 85   | `#0A4A4E` |
| 90   | `#093537` |
| 95   | `#042324` |
| 98   | `#051718` |
| 99   | `#040D0D` |
| 100  | `#000000` |

### accent/yellow

| ステップ | HEX       |
| ---- | --------- |
| 0    | `#FFFFFF` |
| 5    | `#FCFBE2` |
| 10   | `#F9F7C9` |
| 15   | `#F9F2A3` |
| 20   | `#F6EC4F` |
| 25   | `#EBDF19` |
| 30   | `#D3C715` |
| 40   | `#B3A912` |
| 50   | `#9A910A` |
| 60   | `#7F7703` |
| 70   | `#706A10` |
| 80   | `#5B560A` |
| 85   | `#474300` |
| 90   | `#322F04` |
| 95   | `#211E00` |
| 98   | `#161500` |
| 99   | `#0C0B00` |
| 100  | `#000000` |

### neutral

| ステップ | HEX       |
| ---- | --------- |
| 0    | `#FFFFFF` |
| 5    | `#F8F9FA` |
| 10   | `#F2F4F6` |
| 15   | `#ECEFF2` |
| 20   | `#E3E6EA` |
| 25   | `#D6D9DB` |
| 30   | `#C0C3C6` |
| 40   | `#A2A5A8` |
| 50   | `#8A8E92` |
| 60   | `#717579` |
| 70   | `#64686D` |
| 80   | `#505459` |
| 85   | `#3D4146` |
| 90   | `#2A2E32` |
| 95   | `#1B1E21` |
| 98   | `#111417` |
| 99   | `#090B0D` |
| 100  | `#000000` |

### neutral-alpha

| ステップ | HEX         |
| ---- | ----------- |
| 0    | `#00375800` |
| 5    | `#00375807` |
| 10   | `#00234A0D` |
| 15   | `#00234A13` |
| 20   | `#001A3D1C` |
| 25   | `#00112229` |
| 30   | `#010B173F` |
| 40   | `#0109115D` |
| 50   | `#01091175` |
| 60   | `#0109108F` |
| 70   | `#2F3032CC` |
| 80   | `#2F3032E0` |
| 90   | `#1A1E22ED` |
| 100  | `#000000`   |

### white-alpha

| ステップ | HEX         |
| ---- | ----------- |
| 0    | `#FFFFFF00` |
| 5    | `#FFFFFF05` |
| 10   | `#FFFFFF0A` |
| 15   | `#FFFFFF12` |
| 20   | `#FFFFFF1A` |
| 25   | `#FFFFFF21` |
| 30   | `#FFFFFF29` |
| 40   | `#FFFFFF3D` |
| 50   | `#FFFFFF52` |
| 60   | `#FFFFFF7A` |
| 70   | `#FFFFFFA3` |
| 80   | `#FFFFFFCC` |
| 90   | `#FFFFFFEB` |
| 100  | `#FFFFFF`   |

## 4. Lightness-Mode Tokens（ライト / ダーク）

> データソース: Figma Variables — コレクション "Lightness-Mode"、モード "Light" / "Dark_WIP"

Theme Tokensを参照し、モードごとに異なる値に解決されるセマンティックトークン。すべてのUI要素にはこのトークンを使用することで、自動的にダークモード対応が可能になる。

> ⚠️ ダークモードはFigma上で "WIP"（作業中）のステータス — 値は今後変更される可能性あり。

**凡例:** ◀ = ライト / ダークで値が異なるトークン。"エイリアス" 列は参照先のTheme Tokenを示す。

### accent

| トークン                  | Light     | Dark      | エイリアス（Theme参照）                                          |   |
| --------------------- | --------- | --------- | ------------------------------------------------------- | --- |
| accent/link/default   | `#01858B` | `#13767A` | L: color/accent/teal/60 / D: color/accent/teal/70       | ◀ |
| accent/link/disabled  | `#8A8E92` | `#8A8E92` | color/neutral/50                                        |   |
| accent/link/hover     | `#01858B` | `#13767A` | L: color/accent/teal/60 / D: color/accent/teal/70       | ◀ |
| accent/link/visited   | `#1AA1A7` | `#1AA1A7` | color/accent/teal/50                                    |   |
| accent/primary/bold   | `#027E19` | `#A6EEA6` | L: color/accent/primary/70 / D: color/accent/primary/25 | ◀ |
| accent/primary/fixed  | `#028D1D` | `#028D1D` | color/accent/primary/60                                 |   |
| accent/primary/medium | `#028D1D` | `#7BDD7E` | L: color/accent/primary/60 / D: color/accent/primary/30 | ◀ |

### accent-border

|                               | トークン      | Light     | Dark                                                    | エイリアス（Theme参照） |
| ----------------------------- | --------- | --------- | ------------------------------------------------------- | -------------- |
| accent-border/primary/bold    | `#028D1D` | `#A6EEA6` | L: color/accent/primary/60 / D: color/accent/primary/25 | ◀              |
| accent-border/primary/medium  | `#00A724` | `#7BDD7E` | L: color/accent/primary/50 / D: color/accent/primary/30 | ◀              |
| accent-border/primary/subtle  | `#A6EEA6` | `#028D1D` | L: color/accent/primary/25 / D: color/accent/primary/60 | ◀              |
| accent-border/primary/whisper | `#CAF4C9` | `#076615` | L: color/accent/primary/20 / D: color/accent/primary/80 | ◀              |

### accent-container

|                                  | トークン      | Light     | Dark                                                    | エイリアス（Theme参照） |
| -------------------------------- | --------- | --------- | ------------------------------------------------------- | -------------- |
| accent-container/primary/bold    | `#028D1D` | `#00A724` | L: color/accent/primary/60 / D: color/accent/primary/50 | ◀              |
| accent-container/primary/ghost   | `#F3FCF3` | `#002603` | L: color/accent/primary/5 / D: color/accent/primary/95  | ◀              |
| accent-container/primary/heavy   | `#027E19` | `#7BDD7E` | L: color/accent/primary/70 / D: color/accent/primary/30 | ◀              |
| accent-container/primary/medium  | `#00A724` | `#028D1D` | L: color/accent/primary/50 / D: color/accent/primary/60 | ◀              |
| accent-container/primary/soft    | `#A6EEA6` | `#076615` | L: color/accent/primary/25 / D: color/accent/primary/80 | ◀              |
| accent-container/primary/subtle  | `#CAF4C9` | `#01500D` | L: color/accent/primary/20 / D: color/accent/primary/85 | ◀              |
| accent-container/primary/whisper | `#DBF8DB` | `#003906` | L: color/accent/primary/15 / D: color/accent/primary/90 | ◀              |

### border

| トークン                 | Light       | Dark        | エイリアス（Theme参照）                                        |   |
| -------------------- | ----------- | ----------- | ----------------------------------------------------- | --- |
| border/bold          | `#0109108F` | `#FFFFFF7A` | L: color/neutral-alpha/60 / D: color/white-alpha/60   | ◀ |
| border/inverse       | `#FFFFFF`   | `#000000`   | L: color/white-alpha/100 / D: color/neutral-alpha/100 | ◀ |
| border/inverse-fixed | `#FFFFFF`   | `#FFFFFF`   | color/white-alpha/100                                 |   |
| border/inverse-soft  | `#FFFFFFCC` | `#2F3032E0` | L: color/white-alpha/80 / D: color/neutral-alpha/80   | ◀ |
| border/medium        | `#010B173F` | `#FFFFFF52` | L: color/neutral-alpha/30 / D: color/white-alpha/50   | ◀ |
| border/subtle        | `#001A3D1C` | `#FFFFFF29` | L: color/neutral-alpha/20 / D: color/white-alpha/30   | ◀ |
| border/whisper       | `#00234A13` | `#FFFFFF21` | L: color/neutral-alpha/15 / D: color/white-alpha/25   | ◀ |

### feedback

|                           | トークン      | Light     | Dark                                                | エイリアス（Theme参照） |
| ------------------------- | --------- | --------- | --------------------------------------------------- | -------------- |
| feedback/assertive/bold   | `#BE1E12` | `#FFCBC1` | L: color/accent/red/70 / D: color/accent/red/25     | ◀              |
| feedback/assertive/medium | `#DA0900` | `#FFA99A` | L: color/accent/red/60 / D: color/accent/red/30     | ◀              |
| feedback/info/bold        | `#127098` | `#AEE1FF` | L: color/accent/cyan/70 / D: color/accent/cyan/25   | ◀              |
| feedback/info/medium      | `#037FAC` | `#71CFFF` | L: color/accent/cyan/60 / D: color/accent/cyan/30   | ◀              |
| feedback/notice/bold      | `#7D6400` | `#FFE595` | L: color/accent/amber/70 / D: color/accent/amber/20 | ◀              |
| feedback/notice/medium    | `#8D7102` | `#E9BE05` | L: color/accent/amber/60 / D: color/accent/amber/30 | ◀              |
| feedback/positive/bold    | `#006538` | `#9BEFB9` | L: color/accent/green/80 / D: color/accent/green/25 | ◀              |
| feedback/positive/medium  | `#007C45` | `#6EDD9A` | L: color/accent/green/70 / D: color/accent/green/30 | ◀              |

### feedback-border

|                                  | トークン      | Light     | Dark                                                | エイリアス（Theme参照） |
| -------------------------------- | --------- | --------- | --------------------------------------------------- | -------------- |
| feedback-border/assertive/bold   | `#F43828` | `#FFA99A` | L: color/accent/red/50 / D: color/accent/red/30     | ◀              |
| feedback-border/assertive/medium | `#FF735F` | `#BE1E12` | L: color/accent/red/40 / D: color/accent/red/70     | ◀              |
| feedback-border/assertive/subtle | `#FFA99A` | `#7E0601` | L: color/accent/red/30 / D: color/accent/red/85     | ◀              |
| feedback-border/info/bold        | `#129ACF` | `#71CFFF` | L: color/accent/cyan/50 / D: color/accent/cyan/30   | ◀              |
| feedback-border/info/medium      | `#16B2F0` | `#129ACF` | L: color/accent/cyan/40 / D: color/accent/cyan/50   | ◀              |
| feedback-border/info/subtle      | `#71CFFF` | `#064762` | L: color/accent/cyan/30 / D: color/accent/cyan/85   | ◀              |
| feedback-border/notice/bold      | `#AA8A04` | `#E9BE05` | L: color/accent/amber/50 / D: color/accent/amber/30 | ◀              |
| feedback-border/notice/medium    | `#C6A002` | `#AA8A04` | L: color/accent/amber/40 / D: color/accent/amber/50 | ◀              |
| feedback-border/notice/subtle    | `#FFD547` | `#4F3F01` | L: color/accent/amber/25 / D: color/accent/amber/85 | ◀              |
| feedback-border/postive/bold     | `#00A55E` | `#6EDD9A` | L: color/accent/green/50 / D: color/accent/green/30 | ◀              |
| feedback-border/postive/medium   | `#33C176` | `#00A55E` | L: color/accent/green/40 / D: color/accent/green/50 | ◀              |
| feedback-border/postive/subtle   | `#6EDD9A` | `#004F2A` | L: color/accent/green/30 / D: color/accent/green/85 | ◀              |

### feedback-container

|                                      | トークン      | Light     | Dark                                                | エイリアス（Theme参照） |
| ------------------------------------ | --------- | --------- | --------------------------------------------------- | -------------- |
| feedback-container/assertive/bold    | `#DA0900` | `#F43828` | L: color/accent/red/60 / D: color/accent/red/50     | ◀              |
| feedback-container/assertive/ghost   | `#FEF7F6` | `#400000` | L: color/accent/red/5 / D: color/accent/red/95      | ◀              |
| feedback-container/assertive/heavy   | `#BE1E12` | `#FF735F` | L: color/accent/red/70 / D: color/accent/red/40     | ◀              |
| feedback-container/assertive/medium  | `#F43828` | `#BE1E12` | L: color/accent/red/50 / D: color/accent/red/70     | ◀              |
| feedback-container/assertive/soft    | `#FFCBC1` | `#DA0900` | L: color/accent/red/25 / D: color/accent/red/60     | ◀              |
| feedback-container/assertive/subtle  | `#FFDDD8` | `#7E0601` | L: color/accent/red/20 / D: color/accent/red/85     | ◀              |
| feedback-container/assertive/whisper | `#FFE9E4` | `#5A0703` | L: color/accent/red/15 / D: color/accent/red/90     | ◀              |
| feedback-container/info/ghost        | `#F3FBFE` | `#002131` | L: color/accent/cyan/5 / D: color/accent/cyan/95    | ◀              |
| feedback-container/info/medium       | `#129ACF` | `#127098` | L: color/accent/cyan/50 / D: color/accent/cyan/70   | ◀              |
| feedback-container/info/subtle       | `#CFEBFC` | `#085B7E` | L: color/accent/cyan/20 / D: color/accent/cyan/80   | ◀              |
| feedback-container/info/whisper      | `#DDF2FE` | `#053246` | L: color/accent/cyan/15 / D: color/accent/cyan/90   | ◀              |
| feedback-container/notice/ghost      | `#FFF9E6` | `#261D00` | L: color/accent/amber/5 / D: color/accent/amber/95  | ◀              |
| feedback-container/notice/medium     | `#FFD547` | `#7D6400` | L: color/accent/amber/25 / D: color/accent/amber/70 | ◀              |
| feedback-container/notice/subtle     | `#FFE595` | `#665200` | L: color/accent/amber/20 / D: color/accent/amber/80 | ◀              |
| feedback-container/notice/whisper    | `#FFEEBA` | `#392C00` | L: color/accent/amber/15 / D: color/accent/amber/90 | ◀              |
| feedback-container/positive/ghost    | `#F1FDF4` | `#002511` | L: color/accent/green/5 / D: color/accent/green/95  | ◀              |
| feedback-container/positive/medium   | `#00A55E` | `#007C45` | L: color/accent/green/50 / D: color/accent/green/70 | ◀              |
| feedback-container/positive/subtle   | `#C2F5D2` | `#006538` | L: color/accent/green/20 / D: color/accent/green/80 | ◀              |
| feedback-container/positive/whisper  | `#D8F8E2` | `#004F2A` | L: color/accent/green/15 / D: color/accent/green/85 | ◀              |

### on-accent

| トークン                     | Light             | Dark      | エイリアス（Theme参照）                                          |                 |
| ------------------------ | ----------------- | --------- | ------------------------------------------------------- | --------------- |
|                          | on-accent/default | `#FFFFFF` | `#FFFFFF`                                               | color/neutral/0 |
| on-accent/primary/bold   | `#027E19`         | `#A6EEA6` | L: color/accent/primary/70 / D: color/accent/primary/25 | ◀               |
| on-accent/primary/medium | `#028D1D`         | `#7BDD7E` | L: color/accent/primary/60 / D: color/accent/primary/30 | ◀               |

### on-feedback

| トークン                  | Light     | Dark      | エイリアス（Theme参照）                                      |   |
| --------------------- | --------- | --------- | --------------------------------------------------- | --- |
| on-feedback/assertive | `#BE1E12` | `#FFCBC1` | L: color/accent/red/70 / D: color/accent/red/25     | ◀ |
| on-feedback/info      | `#085B7E` | `#AEE1FF` | L: color/accent/cyan/80 / D: color/accent/cyan/25   | ◀ |
| on-feedback/inverse   | `#FFFFFF` | `#FFFFFF` | color/neutral/0                                     |   |
| on-feedback/notice    | `#665200` | `#FFD547` | L: color/accent/amber/80 / D: color/accent/amber/25 | ◀ |
| on-feedback/positive  | `#007C45` | `#9BEFB9` | L: color/accent/green/70 / D: color/accent/green/25 | ◀ |

### on-surface

| トークン                     | Light       | Dark        | エイリアス（Theme参照）                                      |   |
| ------------------------ | ----------- | ----------- | --------------------------------------------------- | --- |
| on-surface/bold          | `#2A2E32`   | `#FFFFFF`   | L: color/neutral/90 / D: color/neutral/0            | ◀ |
| on-surface/ghost         | `#010B173F` | `#FFFFFF3D` | L: color/neutral-alpha/30 / D: color/white-alpha/40 | ◀ |
| on-surface/inverse       | `#FFFFFF`   | `#2A2E32`   | L: color/neutral/0 / D: color/neutral/90            | ◀ |
| on-surface/inverse-fixed | `#FFFFFF`   | `#FFFFFF`   | color/neutral/0                                     |   |
| on-surface/medium        | `#505459`   | `#F2F4F6`   | L: color/neutral/80 / D: color/neutral/10           | ◀ |
| on-surface/subtle        | `#717579`   | `#E3E6EA`   | L: color/neutral/60 / D: color/neutral/20           | ◀ |
| on-surface/whisper       | `#0109115D` | `#FFFFFF3D` | L: color/neutral-alpha/40 / D: color/white-alpha/40 | ◀ |

### shadow

|                | トークン        | Light       | Dark                                                  | エイリアス（Theme参照） |
| -------------- | ----------- | ----------- | ----------------------------------------------------- | -------------- |
| shadow/ambient | `#00234A13` | `#010B173F` | L: color/neutral-alpha/15 / D: color/neutral-alpha/30 | ◀              |
| shadow/border  | `#00234A0D` | `#00234A13` | L: color/neutral-alpha/10 / D: color/neutral-alpha/15 | ◀              |
| shadow/key     | `#00112229` | `#01091175` | L: color/neutral-alpha/25 / D: color/neutral-alpha/50 | ◀              |

### surface

| トークン                         | Light       | Dark        | エイリアス（Theme参照）                                        |   |
| ---------------------------- | ----------- | ----------- | ----------------------------------------------------- | --- |
| surface/base/alternative     | `#F2F4F6`   | `#2A2E32`   | L: color/neutral/10 / D: color/neutral/90             | ◀ |
| surface/base/default         | `#FFFFFF`   | `#000000`   | L: color/neutral/0 / D: color/neutral/100             | ◀ |
| surface/overlay              | `#FFFFFF`   | `#1A1E22ED` | L: color/neutral/0 / D: color/neutral-alpha/90        | ◀ |
| surface/raised/inverse       | `#2A2E32`   | `#F8F9FA`   | L: color/neutral/90 / D: color/neutral/5              | ◀ |
| surface/raised/inverse-fixed | `#2A2E32`   | `#090B0D`   | L: color/neutral/90 / D: color/neutral/99             | ◀ |
| surface/raised/level1        | `#FFFFFF`   | `#1B1E21`   | L: color/neutral/0 / D: color/neutral/95              | ◀ |
| surface/raised/level2        | `#F2F4F6`   | `#2A2E32`   | L: color/neutral/10 / D: color/neutral/90             | ◀ |
| surface/scrim/default        | `#00112229` | `#0109108F` | L: color/neutral-alpha/25 / D: color/neutral-alpha/60 | ◀ |
| surface/scrim/fixed          | `#2F3032E0` | `#2F3032E0` | color/neutral-alpha/80                                |   |
| surface/sunken               | `#F2F4F6`   | `#1B1E21`   | L: color/neutral/10 / D: color/neutral/95             | ◀ |

### surface-container

| トークン                             | Light       | Dark        | エイリアス（Theme参照）                                      |   |
| -------------------------------- | ----------- | ----------- | --------------------------------------------------- | --- |
| surface-container/bold           | `#001A3D1C` | `#FFFFFF52` | L: color/neutral-alpha/20 / D: color/white-alpha/50 | ◀ |
| surface-container/default        | `#FFFFFF`   | `#111417`   | L: color/neutral/0 / D: color/neutral/98            | ◀ |
| surface-container/fixed          | `#FFFFFF`   | `#FFFFFF`   | color/neutral/0                                     |   |
| surface-container/heavy          | `#00112229` | `#FFFFFF7A` | L: color/neutral-alpha/25 / D: color/white-alpha/60 | ◀ |
| surface-container/inverse        | `#3D4146`   | `#F2F4F6`   | L: color/neutral/85 / D: color/neutral/10           | ◀ |
| surface-container/inverse-subtle | `#2F3032E0` | `#FFFFFFCC` | L: color/neutral-alpha/80 / D: color/white-alpha/80 | ◀ |
| surface-container/medium         | `#00234A13` | `#FFFFFF3D` | L: color/neutral-alpha/15 / D: color/white-alpha/40 | ◀ |
| surface-container/medium-opaque  | `#ECEFF2`   | `#3D4146`   | L: color/neutral/15 / D: color/neutral/85           | ◀ |
| surface-container/subtle         | `#00234A0D` | `#FFFFFF29` | L: color/neutral-alpha/10 / D: color/white-alpha/30 | ◀ |
| surface-container/subtle-opaque  | `#F2F4F6`   | `#2A2E32`   | L: color/neutral/10 / D: color/neutral/90           | ◀ |
| surface-container/whisper        | `#00375807` | `#FFFFFF12` | L: color/neutral-alpha/5 / D: color/white-alpha/15  | ◀ |
| surface-container/whisper-opaque | `#F8F9FA`   | `#111417`   | L: color/neutral/5 / D: color/neutral/98            | ◀ |

### opacity

| トークン     | Light | Dark |
| -------- | ----- | ---- |
| disabled | `40`  | `40` |

## 5. Component Tokens（コンポーネントトークン）

> データソース: Figma Variables — コレクション "Component"、モード "Mode 1"

Component TokensはLightness-Mode Tokensを参照する。Lightness-Modeレイヤーを介して、自動的にライト/ダーク値に解決される。以下は**ライトモードでの解決値**。

> ⚠️ **実装上の制約:** Panda CSSはセマンティックトークンの参照を2層までしか解決できない。Theme → Lightness-Mode で1層目を使い切っているため、**Component Tokensはコード上には存在しない**。`@newmo-app/garage-ui-react` のコンポーネントは、Component Tokensの参照先であるLightness-Mode Tokensを直接使って実装されている。以下の「エイリアス（LM参照）」列が、実装時に使用すべきトークンとなる。

### button

| トークン                                            | 値           | エイリアス（LM参照）                                |
| ----------------------------------------------- | ----------- | ------------------------------------------ |
| button/ghost/background/default                 | `#00375800` | color/neutral-alpha/0                      |
| button/ghost/background/hover                   | `#00234A0D` | color/surface-container/subtle             |
| button/ghost/background/pressed                 | `#001A3D1C` | color/surface-container/bold               |
| button/ghost/text                               | `#505459`   | color/on-surface/medium                    |
| button/neutral/background/default               | `#00234A13` | color/surface-container/medium             |
| button/neutral/background/hover                 | `#001A3D1C` | color/surface-container/bold               |
| button/neutral/background/pressed               | `#00112229` | color/surface-container/heavy              |
| button/neutral/text/default                     | `#505459`   | color/on-surface/medium                    |
| button/neutral/text/destractive                 | `#BE1E12`   | color/feedback/assertive/bold              |
| button/primary/background/default               | `#00A724`   | color/accent-container/primary/medium      |
| button/primary/background/destractive/default   | `#F43828`   | color/feedback-container/assertive/medium  |
| button/primary/background/destractive/hover     | `#DA0900`   | color/feedback-container/assertive/bold    |
| button/primary/background/destractive/pressed   | `#BE1E12`   | color/feedback-container/assertive/heavy   |
| button/primary/background/hover                 | `#028D1D`   | color/accent-container/primary/bold        |
| button/primary/background/pressed               | `#027E19`   | color/accent-container/primary/heavy       |
| button/primary/text                             | `#FFFFFF`   | color/on-accent/default                    |
| button/secondary/background/default             | `#DBF8DB`   | color/accent-container/primary/whisper     |
| button/secondary/background/destractive/default | `#FFE9E4`   | color/feedback-container/assertive/whisper |
| button/secondary/background/destractive/hover   | `#FFDDD8`   | color/feedback-container/assertive/subtle  |
| button/secondary/background/destractive/pressed | `#FFCBC1`   | color/feedback-container/assertive/soft    |
| button/secondary/background/hover               | `#CAF4C9`   | color/accent-container/primary/subtle      |
| button/secondary/background/pressed             | `#A6EEA6`   | color/accent-container/primary/soft        |
| button/secondary/text/destractive               | `#BE1E12`   | color/on-feedback/assertive                |

### choice

| トークン                       | 値           | エイリアス（LM参照）                               |
| -------------------------- | ----------- | ----------------------------------------- |
| choice/background/default  | `#00234A0D` | color/surface-container/subtle            |
| choice/background/error    | `#F43828`   | color/feedback-container/assertive/medium |
| choice/background/selected | `#00A724`   | color/accent-container/primary/medium     |
| choice/border/default      | `#010B173F` | color/border/medium                       |
| choice/border/error        | `#F43828`   | color/feedback-border/assertive/bold      |
| choice/border/hover        | `#0109108F` | color/border/bold                         |
| choice/border/selected     | `#00A724`   | color/accent-border/primary/medium        |
| choice/mark/default        | `#FFFFFF`   | color/on-accent/default                   |

### input

| トークン                              | 値           | エイリアス（LM参照）                              |
| --------------------------------- | ----------- | ---------------------------------------- |
| input/background/alternative      | `#00234A0D` | color/surface-container/subtle           |
| input/background/alternativeHover | `#00234A13` | color/surface-container/medium           |
| input/background/error            | `#FEF7F6`   | color/feedback-container/assertive/ghost |
| input/border/error                | `#FF735F`   | color/feedback-border/assertive/medium   |
| input/border/hover                | `#0109108F` | color/border/bold                        |
| input/text/affix                  | `#717579`   | color/on-surface/subtle                  |
| input/text/label                  | `#505459`   | color/on-surface/medium                  |
| input/text/placeholder            | `#0109115D` | color/on-surface/whisper                 |
| input/text/triggerIcon            | `#505459`   | color/on-surface/medium                  |
| input/text/value                  | `#2A2E32`   | color/on-surface/bold                    |

### table

| トークン                            | 値           | エイリアス（LM参照）                                |
| ------------------------------- | ----------- | ------------------------------------------ |
| table/border                    | `#001A3D1C` | color/border/subtle                        |
| table/td/background/error/hover | `#FFE9E4`   | color/feedback-container/assertive/whisper |
| table/td/background/hover       | `#00375807` | color/surface-container/whisper            |
| table/th/background/alternative | `#00375807` | color/surface-container/whisper            |

### サイズ / レイアウトトークン

| トークン                          | 値     |
| ----------------------------- | ----- |
| button/border-width           | `0.5` |
| table/border-width            | `1`   |
| table/inline-gap              | `4`   |
| table/inline-padding          | `12`  |
| table/min-height/condensed    | `32`  |
| table/min-height/medium       | `40`  |
| table/min-height/specious     | `48`  |
| table/padding-block/condensed | `4`   |
| table/padding-block/medium    | `8`   |
| table/padding-block/specious  | `12`  |

## 6. データソースと更新方法

本ドキュメントは2つのソースを統合している：

- **セクション1–2**（Overview, Usage）: zeroheightのデザインドキュメントから手動で整理。設計思想、トークン階層、使用原則、アクセシビリティガイドラインを記述。
- **セクション3–5**（Theme, Lightness-Mode, Component Tokens）: Figma VariablesのJSONエクスポートから自動生成。Figmaで定義された正確なトークン値を含む。

### 更新ワークフロー

1. **設計思想の変更** → zeroheightからセクション1–2を手動更新
2. **トークン値の変更** → Figma Variables JSONを再エクスポート → セクション3–5を再生成
3. **ダークモードの確定** → "Dark_WIP" が確定したら再エクスポート・再生成

### ソースファイル

| ファイル                               | Figmaコレクション    | モード      |
| ---------------------------------- | -------------- | -------- |
| `Theme_newmo_tokens.json`          | Theme          | newmo    |
| `Lightness-Mode_Light_tokens.json` | Lightness-Mode | Light    |
| `Lightness-Mode_Dark_tokens.json`  | Lightness-Mode | Dark_WIP |
| `Component_tokens.json`            | Component      | Mode 1   |