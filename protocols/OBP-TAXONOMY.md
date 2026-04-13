# OBP-TAXONOMY — Taxonomy Primitive（存在分類プリミティブ）

> Status: Draft（PROP-006 条件付き採用、2026-04-13）
> 先行議論: `.bypas/7-thread/platform/260406-vdb-record-type-obp-taxonomy.md`
> Flapbase 実装参照: _impl/ （未作成）
> obp-spec: `taxonomy/genera.yaml` / `taxonomy/species.yaml` / `taxonomy/variants.yaml`

---

## 1. 概念定義

OBP-TAXONOMY は OBP が扱うすべての概念・エンティティを分類する存在論的語彙を定義するプリミティブである。

この Protocol が担う関心事:
「OBP 準拠システムが記録・交換するあらゆる対象が、いずれの genus に属し、どの species として表現されるかを一義的に決定できること」

四大塩基との対応:

```
being.*   → genus: being
resource.* → genus: resource
flow.*     → genus: flow
context.*  → genus: context
```

他 OBP プロトコルとの関係:
- OBP-ACT / OBP-DOC 等はすべて Taxonomy の genus.species 上に構築される
- OBP-TRUST の委譲チェーンは flow.delegate として Taxonomy に従う
- OBP-SEMANTIC-ALIGNMENT とは独立: Taxonomy は「何か」を分類し、SEMANTIC-ALIGNMENT は「どう解釈するか」を扱う

---

## 2. 構造定義

### genus（属）— 4 種固定

OBP の四大塩基に一対一で対応する。genus は OBP 委員会によってのみ定義・変更される。

```
being    — WHO   存在・主体・意志を持つもの
resource — WHAT  being が操作しうる価値・資産・情報
flow     — HOW/WHEN 行動の構造化された記述・時間軸上の進行
context  — WHERE/WHY 行動の背景となる意味の場・境界・状況
```

### species（種）— genus ごとに定義

species は `genus.species` のドット区切り表記で参照する。

```
species 記法の例:
  being.person         人間の主体
  being.agent          AIエージェント
  being.organization   組織体
  resource.document    文書・記録
  resource.money       金銭・通貨単位の価値
  resource.knowledge   知識・情報資産
  flow.task            実行すべき作業単位
  flow.schedule        時間軸上の予定・イベント
  flow.commerce        商取引の進行
  context.workspace    活動の境界・場
  context.time.event   時間的に境界された出来事
  context.market       取引が成立する意味の場
```

species の完全一覧は obp-spec の `taxonomy/species.yaml` を SSOT とする。

### variant（変種）— species の細分化

variant は `genus.species.variant` の3段階表記。

```
例:
  flow.task.recurring     繰り返しタスク
  resource.document.invoice  請求書
```

variant は obp-spec の `taxonomy/variants.yaml` に定義する。
species に比べ変化頻度が高いため、variant のみコミュニティ提案で追加できる。

---

## 3. 拡張 namespace 機構

OBP コア taxonomy で定義されない概念を各実装が独自に追加できる拡張機構を提供する。

### 拡張記法

```
genus.ext.{provider}.{term}

例:
  flow.ext.flapbase.sprint_goal    Flapbase 固有のスプリント目標
  resource.ext.flapbase.canvas     Flapbase のキャンバスオブジェクト
  context.ext.acme.territory       ACME 社固有の営業区域
```

### ルール

- コア namespace（genus.species 等）は obp-spec に定義する。変更には PROP が必要
- `ext.{provider}` 以下は各実装が独自に定義できる。OBP 委員会の承認は不要
- 異なる実装間でデータを交換するとき、ext namespace の値は「実装固有」として扱われ、受信側での意味保証は行わない
- コア昇格を希望する ext species は PROP を提出して審議する

この機構により、OBP エコシステムへの参加障壁を下げつつコア語彙の安定性を保つ。

---

## 4. 参照形式（obp-spec との関係）

OBP-TAXONOMY は obp-spec リポジトリの以下ファイルを規範的ソースとする:

```
obp-spec/taxonomy/
  genera.yaml       — 4 genus の定義（変更には PROP 必要）
  species.yaml      — genus × species の全一覧
  variants.yaml     — species × variant の拡張定義
  README.md         — 拡張 namespace 規約・コア昇格手順
```

YAML を規範フォーマットとし、JSON Schema は genera.yaml / species.yaml から自動生成する。

---

## 5. 他プロトコルとの関係

- 依存: なし。Taxonomy は OBP の基底プリミティブ
- 補完: OBP-ACT（action の対象を Taxonomy で分類）/ OBP-DOC（document の genre を Taxonomy で表現）/ OBP-TRUST（委譲対象を flow.task として識別）
- 排他: OBP-SEMANTIC-ALIGNMENT とは補完関係。Taxonomy は「何か」を定め、SEMANTIC-ALIGNMENT は「どう解釈するか」を定める

---

## 6. 不変則（Invariants）

- genus は 4 種から増減しない（四大塩基と一対一対応を保つ）
- `genus.species` 表記は OBP 準拠システム間で同一の意味を持つ
- ext namespace の値は実装固有であり、OBP コアとしての意味保証を持たない
- コア genus/species の変更は必ず PROP を経由する
