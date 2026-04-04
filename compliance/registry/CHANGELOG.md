# OBP Compliance Registry — Changelog

---

## v0.1.0 — 2026-04-03

初版リリース。PROP-071（accepted）に基づき構造を確定。

追加:
  - SCHEMA.md（フィールド仕様・AXIS定義・CAL定義）
  - AXIS-CRITERIA.md（判定基準・新規ルール追加フロー）
  - rules/EU_AI_ACT_ART5.yaml（禁止行為8種 CAL-4）
  - rules/EU_AI_ACT_ANNEX3.yaml（高リスク5種 CAL-3）
  - rules/EU_AI_ACT_ART50.yaml（透明性義務2種 CAL-1）
  - rules/GDPR_ART22.yaml（自動化意思決定2種 CAL-3）
  - rules/JP_PIPA_2022.yaml（要配慮個人情報2種 CAL-3）
  - safety/CONTENT_SAFETY_UNIVERSAL.yaml（常時フィルター3種）
  - safety/CONTENT_SAFETY_CONTEXT.yaml（文脈依存フィルター4種）
  - tags/COMPLIANCE_TAGS.yaml（28タグ正規定義）

フィールド: axis / axis_proximity / source_type を全エントリに追加
