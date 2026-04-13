# OBP-SEMANTIC-ALIGNMENT — Semantic Alignment Protocol（意味的整合プリミティブ）

> Status: Draft（PROP-009 条件付き採用、2026-04-13）
> 先行議論: PROP-009 / SI-SPEC.md / LEXICON-SPEC.md
> Flapbase 実装参照: _impl/（未作成）
> obp-spec: `profiles/`

---

## 1. 概念定義

OBP-SEMANTIC-ALIGNMENT は、OBP 準拠システム間で意思・データを交換するとき、
「単位・日付・数値の解釈」が一致することを保証するプロトコルである。

この Protocol が担う関心事:
「Owner が意思を表明するとき使った単位・慣習を、エージェントが正確に解釈できること」

設計原則:
OBP は意味的解釈の実体を自前で定義しない。既存の外部標準を SSOT として参照し、
「概念の名前と参照先」のみを OBP として定義する（参照-定義しないパターン）。

四大塩基との対応:

```
context.*  — どの「意味の場」で解釈するか（market / locale / jurisdiction）
flow.*     — いつを基準にするか（fiscal_year / quarter / week_start）
resource.* — 何の単位で測るか（currency / physical_unit / numeric_modifier）
```

他 OBP プロトコルとの関係:
- OBP-COMMERCE の商取引語彙（PROP-010）と連携: net/gross の「何を控除するか」の定義はプロファイルが担う
- OBP-COUNTERPARTY の取引先 governance とは補完関係: COUNTERPARTY は「取引先のルール」を記述し、SEMANTIC-ALIGNMENT はその解釈基準を提供する
- OBP-TAXONOMY とは独立: TAXONOMY は「何か」を分類し、SEMANTIC-ALIGNMENT は「どう解釈するか」を定める

---

## 2. 構造定義

### Profile Schema（プロファイル構造）

OBP 準拠システムは使用する Semantic Profile を宣言する。

```yaml
profile: {profile_name}    # global | us | ja | {custom}
_version: string           # バージョン識別子（例: "global@1.0" / "ja@2026-04"）
_overrides_hash: string | null
  # Workspace 独自 override がある場合、その内容の SHA-256 短縮形
  # override なし = null（純粋なプロファイル宣言）
  # override あり = "sha256:a3f2..." 形式
overrides:                 # 省略可能。個別フィールドの上書き
  {field}: {value}
```

`_version` と `_overrides_hash` はデータ交換時に送受信側が意味的整合を検証するためのメタデータ。
受信側が override 内容を解釈できない場合は変換エラーとして処理し、サイレントな誤計算を防ぐ。

### Concept Vocabulary（標準概念名）

OBP が自前で書くのは「標準概念名のカタログ」のみ。値の定義は外部標準に委ねる。

```
fiscal_quarter       — 会計四半期（解釈はプロファイルに委ねる）
fiscal_year_end      — 会計年度末
week_start_day       — 週の開始曜日
numeric_modifier     — gross / net 等の数値修飾（詳細: OBP-COMMERCE）
fx_rate_convention   — 為替レートの計算基準
comparison_unit      — 比較の基準単位
org_scope            — 組織スコープ（全社 / 部門 / WS）
```

### 外部標準委譲一覧

OBP が参照するが自前定義しない標準:

| ドメイン | 概念 | 委譲先 |
|---------|------|--------|
| 時間 | 日時フォーマット | ISO 8601 |
| 通貨 | 通貨コード | ISO 4217 |
| 地理 | 国・地域コード | ISO 3166-1 alpha-2 |
| 言語 | 言語タグ | IETF BCP 47 |
| 数量 | 物理量単位 | SI 単位系 / ISO 80000 |
| 法律 | 税率・税法 | 各国税法（参照名のみ） |
| 法律 | データ保護 | GDPR / 個人情報保護法 |
| 会計 | 会計基準 | US-GAAP / IFRS / J-GAAP |
| 貿易 | 貿易条件 | INCOTERMS（ICC） |

---

## 3. 同梱プロファイル

OBP が初期提供する3プロファイル。obp-spec の `profiles/` に定義する。

```yaml
# global — 国際標準の素直な適用
profile: global
_version: "global@1.0"
references:
  temporal: ISO 8601
  currency: ISO 4217
  country: ISO 3166-1 alpha-2
  locale_tag: IETF BCP 47
conventions:
  fiscal_year: calendar_year
  quarter: [Jan-Mar, Apr-Jun, Jul-Sep, Oct-Dec]
  week_start: monday
  fx_default: spot_rate

---

# us — 米国商慣習
profile: us
_version: "us@1.0"
extends: global
locale: en-US
overrides:
  week_start: sunday
  accounting_ref: US-GAAP
  tax_ref: IRS

---

# ja — 日本商慣習
profile: ja
_version: "ja@1.0"
extends: global
locale: ja-JP
overrides:
  fiscal_year:
    start_month: 4
    end_month: 3
    reference: "法人税法（企業定款により異なる場合あり）"
  quarter:
    q1: [Apr, May, Jun]
    q2: [Jul, Aug, Sep]
    q3: [Oct, Nov, Dec]
    q4: [Jan, Feb, Mar]
  week_start: sunday_or_monday
  accounting_ref: J-GAAP
  fx_default: period_end_rate
```

### プロファイル間のデータ変換

OBP 準拠システム間でデータを交換するとき、プロファイル宣言から変換ルールを機械的に導出する。

```
例:
  送信側 (profile: ja) fiscal Q1 = Apr-Jun
  受信側 (profile: us) fiscal Q1 = Jan-Mar

  変換エンジンの処理:
    ja.quarter.q1 (Apr-Jun) → us.quarter.q2 (Apr-Jun) として受信側に変換
```

---

## 4. 企業内 Governance との境界

以下は OBP-SEMANTIC-ALIGNMENT のスコープ外:

```
承認権限マトリクス（誰が何まで承認できるか）
内部用語の定義（この会社での「完了」「承認済み」の意味）
意思決定プロトコル
```

これらは Owner Sovereignty が Workspace 内運用に落ちた形であり、
Flapbase の Semantic Layer（S2 behavioral / S1 structural）が蓄積・参照する対象となる。

---

## 5. 他プロトコルとの関係

- 依存: OBP-TAXONOMY（プロファイルが対象とする concept の genus.species を参照）
- 補完: OBP-COMMERCE（numeric_modifier の解釈基準を提供）/ OBP-COUNTERPARTY（取引先の税・会計基準の参照先を提供）
- 排他: OBP-PROFILE（PROFILE は being の感情・認知プロファイル。SEMANTIC-ALIGNMENT はデータ解釈の慣習的整合）

---

## 6. 不変則（Invariants）

- OBP は概念の名前と外部標準への参照先を定義する。値の定義は外部標準に完全委ねる
- プロファイル宣言には `_version` を必ず含める
- override がある場合 `_overrides_hash` を必ず含める。受信側は非 null の hash を検証する義務を持つ
- 企業内 Governance（承認権限マトリクス等）は OBP スコープ外
