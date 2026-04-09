# 公式 Figma MCP（use_figma）vs Figma Console MCP 比較メモ

> 内部メモ。`drafts/` 配下なので Mintlify のビルドには出ない。
> 将来 Figma 側のアップデートで状況が変わったときの比較ベースラインとして残す。

## 調査日 / 背景

- **調査日**: 2026-04-09
- **きっかけ**: 公式 Figma MCP が `use_figma` ツールで Figma への書き込みをサポートし始めた。これまで Garage Design System のドキュメント自動化は Figma Console MCP（Plugin API ベースの非公式ツール）で実装してきたが、公式に移行できるなら一本化したい。
- **判断したいこと**: `skills/` 配下の自動化スキル群を公式 MCP に移行できるか、併用すべきか、据え置きすべきか。

## 機能対応表

| 領域 | Figma Console MCP | 公式 Figma MCP | 対応 |
|---|---|---|---|
| ノード作成・編集・削除 | `figma_create_child` / `figma_set_text` / `figma_delete_node` 等 | `use_figma`（JS 実行で実現） | 〇 |
| Auto Layout / Frame 構築 | 専用ツール群 | `use_figma` 内で可能 | 〇 |
| コンポーネント instantiate | `figma_instantiate_component` + `figma_set_instance_properties` | `use_figma` 内で可能 | 〇 |
| Variables 読み取り | `figma_get_variables` | `get_variable_defs` | 〇 |
| **Variables バッチ作成・更新** | `figma_batch_create_variables` / `figma_batch_update_variables`（最大 50 件） | バッチ専用 API なし（個別ループで対応） | △ |
| **Annotations 作成・設定** | `figma_set_annotations` ✅ | **未サポート** ✕ | ✕ |
| Annotations 読み取り | `figma_get_annotations` ✅ | 不明・確認できず | ✕ |
| **画像エクスポート** | `figma_get_component_image` → **URL 直返し（30 日有効）** | `get_screenshot` → **base64**（既知の MIME 不整合報告あり） | △ |
| **Plugin API raw access** | `figma_execute`（任意の Plugin API を実行可能） | なし | ✕ |
| Code Connect | `figma_get_code_connect_map` 等 | `get_code_connect_map` 等（こちらは公式の方が一級市民） | 〇（公式優位） |
| Design System 検索 | `figma_search_components` | `search_design_system` | 〇 |
| Design Context 取得 | `figma_get_component_for_development` | `get_design_context` | 〇 |
| Lint / Accessibility | `figma_lint_design` / `figma_audit_*` | なし | ✕ |

### 強調すべき差分

1. **Annotations 作成は公式に存在しない** — このリポの自動化にとって致命傷
2. **画像エクスポートが URL 直返しか base64 か** — 体感速度と扱いやすさが激変
3. **バッチ系 API がない** — 大量 Variables 同期の現実的な性能
4. **`figma_execute` 相当がない** — 抜け穴がないので、公式 MCP がサポートしない操作は永遠にできない

## 画像エクスポートの実態

| | Console MCP | 公式 MCP |
|---|---|---|
| ツール | `figma_get_component_image` | `get_screenshot` |
| 形式 | PNG / JPG / SVG / PDF | PNG のみ |
| 返却方式 | **CDN URL を直接返す**（30 日間有効） | **base64 エンコード**（または不明確） |
| 既知の不具合 | 特になし | Figma Forum で MIME type 不整合の報告あり（image/jpeg 宣言なのに PNG が返ってくる） |
| 実装パターン | URL を MDX に直接挿入できる | base64 → デコード → PNG ファイル化、の中間処理が必要 |

`generate-anatomy.md` / `generate-variants-visual.md` / `generate-color-annotation.md` の中核動作はすべてこの画像書き出しに依存しているため、ここで方式が劣化すると自動化全体が遅くなる。

## skills/ ごとの移行可能性判定

| Skill | 公式 MCP のみで再実装可能か | 主な理由 |
|---|---|---|
| `generate-anatomy.md` | △ 部分的に可能 | フレーム生成・テキスト編集は `use_figma` で代替可能。ただし最終ステップの画像書き出しが base64 経由になり、実用性が落ちる |
| `generate-variants-visual.md` | ✕ 不可 | 画像書き出しの劣化に加え、大量バリアントフレームの一括生成・配置で `figma_execute` 相当の低レベル操作と Plugin API 全機能アクセスがほしい場面が多い |
| `generate-color-annotation.md` | **✕ 原理的に不可** | このスキルの本体は色アノテーションを Figma 上に書き込むこと。公式 MCP は Annotations 作成を一切サポートしていない |
| `generate-token-reference.md` | 〇 ほぼ可能 | Variables を読み取って Markdown テーブルを再生成するだけなので、`get_variable_defs` で代替可能。ただし `figma_get_variables` の細かいフィルタオプションは失う |

## 結論

**Console MCP 主軸を維持、公式 MCP は限定的に併用** が現実的。

- `generate-color-annotation.md` が公式 MCP では原理的に成立しないのが決定打
- `generate-anatomy` / `generate-variants-visual` も画像書き出しが base64 に降格する時点で実用に耐えない
- `generate-token-reference.md` は単独では公式に移せるが、わざわざ二つの MCP を行き来するコストの方が高い
- **Code Connect 系の新規作業のときだけ公式 MCP を併用する** という温度感がよさそう（こちらは公式の方が一級市民）

## 将来の見直しトリガー

以下のいずれかが起きたら、このメモを見返してもう一度判断する。

1. **公式 `use_figma` が Annotations 作成をサポートしたら** → `generate-color-annotation.md` を真っ先に再評価
2. **`get_screenshot` が URL 直返しに改善されたら** → 画像系スキル（anatomy / variants-visual / color-annotation）を全部再評価
3. **公式に Variables バッチ API が入ったら** → `generate-token-reference.md` の移行を本格検討
4. **Console MCP 側がメンテされなくなったら** → 移行可否に関係なく公式に寄せる前提で設計を見直す

## 参考リンク

- Figma MCP Server 公式ドキュメント: https://developers.figma.com/docs/figma-mcp-server/
- Tools and Prompts: https://developers.figma.com/docs/figma-mcp-server/tools-and-prompts/
- Figma Help Center — Guide to the Figma MCP server: https://help.figma.com/hc/en-us/articles/32132100833559-Guide-to-the-Figma-MCP-server
- Figma Console MCP docs: https://docs.figma-console-mcp.southleft.com/
