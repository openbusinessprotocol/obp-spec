# OBP Compliance Registry — Schema Definition

version: 0.1.0
updated: 2026-04-03
source: PROP-071（accepted）

---

## 概要

本 Registry は OBP-CORE-V1 §context.prohibition（PROP-017）が定義する
禁止の分類軸（AXIS-1〜4）に対する具体的ルールを収録する。

```
OBP Core（不変）: 禁止の分類軸（AXIS）と判定インターフェースを定義
OBP Compliance Registry（本フォルダ）: 各 AXIS の具体的ルールを制定
実装プラットフォーム: Registry を参照し CCE・OCS として実行する
```

---

## AXIS 定義

| AXIS | 名称 | 説明 | CCE 対象 |
|------|------|------|:-------:|
| AXIS-1 | 不可逆性の絶対閾値 | 行為の結果が当事者・第三者にとって不可逆な被害をもたらす | ✅ |
| AXIS-2 | 責任の消失 | 行為の結果として誰の意思・責任かが追跡不可能になる | ✅ |
| AXIS-3 | Sovereignty の再委譲 | Owner が自らの主権的地位を放棄・譲渡する行為 | ❌（プロトコル層で制御）|
| AXIS-4 | OBP プロトコル攻撃 | Identity_Anchor / Integrity_Beacon / Contract_Isolation の破壊 | ❌（runtime 異常検知で制御）|

AXIS-3/4 は compliance_tags による設計時評価（CCE）の対象外。
OBP-CORE の三つの不変インターフェースが直接制御する。

---

## axis_proximity 定義

| 値 | 意味 | CAL | 解除可否 |
|----|------|:---:|:-------:|
| `direct` | AXIS の定義に直接抵触 | CAL-4 | 不可（flow.delegate 成立不可）|
| `adjacent` | AXIS に抵触するリスクあり。文脈・ガードレール次第で許容可 | CAL-3 | occ.risk_appetite / ws.output_safety_context で制御可 |

---

## CAL（Compliance Action Level）定義

| CAL | 状態 | 根拠 |
|:---:|------|------|
| CAL-4 | Delegation Void | AXIS 直接違反（axis_proximity: direct）。委譲不成立 |
| CAL-3 | Review Required | AXIS 隣接領域（axis_proximity: adjacent）。人間承認で成立可 |
| CAL-2 | Cleared | AXIS 違反なし。委譲成立 |
| CAL-1 | Advisory | AXIS 違反なし。ただし AXIS-2 予防的透明性要件あり（開示マーキング必要）|

---

## source_type 定義

| 値 | 意味 | Registry 収録 |
|----|------|:------------:|
| `obp_curated` | OBP 委員会がキュレーション（現段階のデフォルト）| ✅ |
| `external_reference` | 外部権威機関の公式レジストリを参照 | ✅ |
| `platform_custom` | 実装プラットフォーム固有のルール | ❌（ローカルのみ）|

`platform_custom` は Registry に収録しない。
OBP 互換エージェント間のルールセット一貫性を維持するため。

---

## rules/*.yaml フィールド仕様

```yaml
- id: string                  # 規制条文を含む一意 ID（例: ART5_1_F）
  name: string                # 英語名称
  name_ja: string             # 日本語名称
  axis: string[]              # 対応 AXIS（例: [AXIS-1, AXIS-2]）
  axis_proximity: direct|adjacent
  tags_all: string[]          # AND 条件（全て含む場合にマッチ）
  tags_any: string[]          # OR 条件（いずれか含む場合にマッチ。空なら無視）
  tags_exclude: string[]      # 除外条件（含む場合にマッチ解除）
  cal: CAL-1|CAL-2|CAL-3|CAL-4
  source_type: obp_curated|external_reference
  regulation: string          # 例: EU_AI_ACT
  regulation_article: string  # 例: "5(1)(f)"
  effective_from: date        # ISO 8601
  applies_to_wf_types: string[]  # デフォルト: [ai, mechanical, scheduled, webhook]
```

## safety/*.yaml フィールド仕様

```yaml
- id: string
  name: string
  name_ja: string
  axis: string[]
  axis_proximity: direct|adjacent
  applies_to: string[]        # [image, video, text, audio]
  activation: always|context_dependent
  activation_contexts: string[]    # axis_proximity: adjacent のみ
  deactivation_contexts: string[]  # axis_proximity: adjacent のみ
  action: block|block_and_report
  source_type: obp_curated|external_reference
  provider_expected: boolean  # true = プロバイダーが第一義的に対応
```

---

## ファイル構成

```
compliance/
  registry/
    rules/
      EU_AI_ACT_ART5.yaml       AXIS-1/2 direct   CAL-4（禁止行為8種）
      EU_AI_ACT_ANNEX3.yaml     AXIS-2 adjacent   CAL-3（高リスク）
      EU_AI_ACT_ART50.yaml      AXIS-2 advisory   CAL-1（透明性義務）
      GDPR_ART22.yaml           AXIS-2            CAL-3
      JP_PIPA_2022.yaml         AXIS-2            CAL-3
    safety/
      CONTENT_SAFETY_UNIVERSAL.yaml   AXIS-1 direct    activation: always
      CONTENT_SAFETY_CONTEXT.yaml     AXIS-1 adjacent  context-dependent
    tags/
      COMPLIANCE_TAGS.yaml      compliance_tags 正規定義
    AXIS-CRITERIA.md            各 AXIS の判定基準・tags マッピング
    SCHEMA.md                   本ファイル
    CHANGELOG.md
```
