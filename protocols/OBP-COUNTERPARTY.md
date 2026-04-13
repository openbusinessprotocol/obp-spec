# OBP-COUNTERPARTY — Counterparty Governance Protocol（取引先統治プロトコル）

> Status: Draft（PROP-011 条件付き採用、2026-04-13）
> 先行議論: PROP-011 / inbox/20260412-workspace-governance-profile.md
> Flapbase 実装参照: _impl/（未作成）
> obp-spec: `schemas/counterparty-governance-core.yaml` / `profiles/ja/counterparty-governance-extension.yaml`
> 依存: OBP-SEMANTIC-ALIGNMENT（プロファイル機構）/ PROP-012（privacy_level / Authorization）

---

## 1. 概念定義

OBP-COUNTERPARTY は、取引先企業との商取引における governance 情報（締め日・支払条件・書類フロー・税・手数料等）を型として定義し、デファクトな参照先を示すプロトコルである。

この Protocol が担う関心事:
「エージェントが取引先の governance を理解し、取引成立時に経理・書類処理を完全自動化できること」

四大塩基との対応:

```
context.* — 取引先という「意味の場」（governance の宣言元・ルールの境界）
flow.*    — 支払サイクル・書類フロー（締め日 → 支払日 → 仕訳の時間軸進行）
resource.* — 請求書・支払調書・KYC 文書（governance が生成する資源）
```

OBP-COMMERCE との関係:
- OBP-COMMERCE は flow.commerce の「イベント仕様」を定義する
- OBP-COUNTERPARTY は各イベントを「どの governance 条件で実行するか」を定義する
- 完全な BAAB 取引 = OBP-COMMERCE（何をするか）+ OBP-COUNTERPARTY（どの条件でするか）

---

## 2. 構造定義

### privacy_level の定義

counterparty_governance の各フィールドには privacy_level を付与する。

```
private              — Owner のみ参照可能
workspace_internal   — Workspace メンバー内に限定
counterparty_shared  — 取引先と共有される情報
```

この分類は PROP-012（VDB Authorization Primitive）の Owner/Operator/Viewer ロール体系と接続する。

### A-1. OBP コアスキーマ（国際汎用）

obp-spec の `schemas/counterparty-governance-core.yaml` を SSOT とする。

```yaml
payment_cycle:
  invoice_cut_off_day: integer | "end"    # 締め日
    privacy_level: workspace_internal
  payment_day: integer | "end"            # 支払日
    privacy_level: workspace_internal
  payment_month_lag: integer              # 支払月ラグ（1=翌月）
    privacy_level: workspace_internal
  reference: string                       # 根拠（契約書 / 慣習）
    privacy_level: workspace_internal

document_flow:
  invoice_issuer: "seller" | "buyer" | "none"
    privacy_level: counterparty_shared
  payment_advice_issuer: "buyer" | "seller" | "none"
    privacy_level: counterparty_shared

tax:
  tax_treatment: "inclusive" | "exclusive"
    privacy_level: counterparty_shared
  tax_id_type: string | null              # 各国税法に従った種別
    privacy_level: workspace_internal
  tax_id_collected: boolean               # 取得完了状態。実体は外部 SaaS に委任
    privacy_level: workspace_internal

fees:
  transfer_fee_bearer: "sender" | "receiver" | "split"
    privacy_level: workspace_internal

withholding:
  applicable: boolean
    privacy_level: workspace_internal
  rate: decimal | null                    # 参照: 各国所得税法。値は法に委ねる
    privacy_level: workspace_internal
  declaration_required: boolean
    privacy_level: workspace_internal

regulatory_compliance:
  kyc_status: "not_required" | "pending" | "completed" | "expired"
    # 参照: 各国 AML 規定 / FATF recommendations。実体は外部 SaaS 管理
    privacy_level: workspace_internal
  kyc_verified_at: ISO8601 | null
    privacy_level: workspace_internal
```

### A-2. ja プロファイル拡張（JP 法人固有）

`profile: ja`（OBP-SEMANTIC-ALIGNMENT）適用時のみ有効。
obp-spec の `profiles/ja/counterparty-governance-extension.yaml` を SSOT とする。

```yaml
tax:
  qualified_invoice_registered: boolean
    # 参照: 国税庁インボイス制度（適格請求書等保存方式）
    privacy_level: counterparty_shared
  registration_number: string | null      # T + 13桁
    privacy_level: counterparty_shared
  reduced_rate_applicable: boolean        # 軽減税率（8%）対象取引を含むか
    privacy_level: workspace_internal

fees:
  wire_transfer_fee_mode: "sender_deduct" | null
    # 差し引き送金（全銀協慣習固有パターン）
    # 参照: 全銀協標準
    privacy_level: workspace_internal

withholding:
  rate_reference: string                  # 例: "所得税法第204条"
    privacy_level: workspace_internal

regulatory_compliance:
  tax_id_type_ja: "my_number" | null
    # 参照: マイナンバー法 / 法人番号法
    privacy_level: workspace_internal
```

---

## 3. Override 階層

OBP-COUNTERPARTY が定義するのは「override の構造スキーマ」のみ。
保存方法・有効期間管理は World Filter（実装プラットフォーム）の責任範囲。

```
counterparty default（取引先デフォルト governance）
  ↓ 上書き可能（contract 単位）
contract override
  例: 通常は翌月末払いだが、この案件は検収後30日
  ↓ 上書き可能（transaction 単位）
transaction override
  例: この請求のみ分割払い
```

原則: エージェントは最も狭いスコープの override 値を適用する。

---

## 4. 取得モード（OBP スコープの境界）

OBP が定義する取得モードは Protocol（宣言型）のみ:

```
Protocol（宣言型）
  取引先が OBP 準拠の counterparty_governance を公開・提供する
  → 受け取り側はその宣言を OBP 型として解釈する
```

Learning（学習型 — 過去書類からの自動抽出）は Flapbase の実装機能であり OBP スコープ外。
現実世界では大半が Learning モードから始まり、OBP 準拠化が進むにつれ Protocol モードに移行する。

---

## 5. 自動化される処理（参考）

counterparty_governance が既知の場合、取引成立時に以下が完全自動化できる（Flapbase 実装層）:

```
1. 請求書・支払調書の生成タイミング計算（invoice_cut_off_day + payment_month_lag）
2. 請求書フォーマット選択（ja profile: qualified_invoice_registered による切り替え）
3. 消費税額計算（tax_treatment + 軽減税率フラグ）
4. 源泉徴収額計算（withholding.applicable + rate）
5. 振込金額確定（transfer_fee_bearer に基づく調整）
6. KYC・税務番号の収集状態確認（支払前アラート）
7. 仕訳の自動生成
```

---

## 6. 他プロトコルとの関係

- 依存: OBP-SEMANTIC-ALIGNMENT（プロファイル機構でコアと ja 拡張を切り替える）
- 依存: PROP-012 Authorization Primitive（privacy_level による情報アクセス制御）
- 補完: OBP-COMMERCE（flow.commerce イベントを実行するときの governance 条件を提供）
- 排他: OBP-TRUST（TRUST は runtime 委譲権限。COUNTERPARTY は取引先の static な governance 型定義）

---

## 7. 不変則（Invariants）

- コアスキーマは国際汎用フィールドのみを定義する。国固有フィールドはプロファイル拡張に委ねる
- 各フィールドには privacy_level を付与する（省略不可）
- OBP が定義するのは型のスキーマと参照先のみ。取得方法・保存方法は実装層の責任
- override 適用の原則: 最も狭いスコープを使う
