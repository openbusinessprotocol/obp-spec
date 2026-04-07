# AXIS Judgment Criteria

version: 0.1.0
updated: 2026-04-03
source: PROP-071（accepted）

---

## 概要

OBP-CORE-V1 §context.prohibition が定義する4分類軸の判定基準。
新規ルール追加時にどの AXIS に該当するかを判定する際の参照基準として使用する。

---

## AXIS-1: 不可逆性の絶対閾値

定義: 行為の結果が当事者・第三者にとって不可逆な被害をもたらす。

判定基準:

```
以下のいずれかに該当する場合、AXIS-1 に抵触する。

直接違反（axis_proximity: direct）:
  - 行為の結果を技術的・社会的に取り消すことが不可能
  - 人間の基本的尊厳・身体的安全を不可逆に侵害する
  - 児童・社会的弱者への構造的被害（CSAM 等）
  - 国際人道法が規定する人道に対する罪に該当

隣接領域（axis_proximity: adjacent）:
  - 適切なガードレール（文脈制御・人間承認）があれば不可逆性が軽減される
  - 被害が発生しうるが、文脈によっては正当な用途が存在する
  - 例: 暴力描写（医療・学術用途と娯楽用途で評価が分かれる）
```

compliance_tags との対応（AXIS-1 直接違反):

```
face_recognition + real_time + public_space → リアルタイム生体追跡（不可逆的監視）
emotion_inference + workplace/education → 職場・教育での感情搾取
vulnerability_exploit → 脆弱者への操作（不可逆的心理被害）
CSAM / terrorism_incitement（OCS） → 人道的最小値の絶対違反
```

---

## AXIS-2: 責任の消失（Identity Chain の遮断）

定義: 行為の結果として、誰の意思・責任かが追跡不可能になる。

判定基準:

```
以下のいずれかに該当する場合、AXIS-2 に抵触する。

直接違反（axis_proximity: direct）:
  - 意思決定の責任主体が完全に不明確になる
  - Identity Chain が構造的に遮断される
  - OBP Avatar Principle 上、責任帰属が100%追跡不可能になる

隣接領域（axis_proximity: adjacent）:
  - 責任帰属が曖昧になるリスクがあるが、透明性要件を満たせば許容可能
  - 人間の最終確認・開示義務によって責任の連鎖が維持できる
  - 例: 高リスク自動化意思決定（人間承認があれば成立可）
        AI生成コンテンツ（開示マーキングがあれば成立可）
```

compliance_tags との対応:

```
social_scoring → 国家・機関が AI に責任を押しつける構造
automated_decision + binding_decision → 責任の所在が不明確な拘束的判断
synthetic_identity → 出力の責任主体が偽装される（CAL-1: 開示義務）
profiling + automated_decision → 個人への影響の責任追跡が困難
```

CAL-1 との関係:

```
AXIS-2 隣接に留まる場合でも、透明性要件（責任帰属の明確化）が義務付けられる。
これが CAL-1（Advisory）の根拠。
EU AI Act Art.50 の開示義務は AXIS-2 の予防的措置として位置づける。
```

---

## AXIS-3: Sovereignty の再委譲

定義: Owner が自らの主権的地位（Identity Anchor）を放棄・譲渡する行為。

判定基準:

```
以下のいずれかに該当する場合、AXIS-3 に抵触する。

- Owner が自分の Identity Anchor を別の主体に移譲する
- Owner がエージェントに対して、Owner 自身の主権的地位を第三者に再委譲する権限を与える
- flow.delegate の受任者が、授権者の同意なく第三者に主権的権限を再委譲する

注: OBP-TRUST による機能的権限（Capabilities）の委譲チェーン
    （Primary Agent → Sub-Agent → Execution）は AXIS-3 の対象外。
    「委譲可能な機能範囲が授権者を超えない」制約は OBP-TRUST §7 が担保する。
```

CCE 設計時評価の対象外:

```
AXIS-3 は compliance_tags による設計時評価では検出できない。
flow.delegate プロトコルの構造自体が主権的権限の再委譲を禁止する。
  - flow.delegate の成立条件 A: Owner が明示的に授権している
  - 受任者への主権的権限の再委譲はプロトコル上不可

したがって compliance registry にルールを記述する必要がない。
OBP-CORE Contract_Isolation が直接制御する。
```

---

## AXIS-4: OBP プロトコル自体への攻撃

定義: Identity_Anchor / Integrity_Beacon / Contract_Isolation の破壊。

判定基準:

```
以下のいずれかに該当する場合、AXIS-4 に抵触する。

- エージェントが自分の Root Key 紐付けを切断する試みをする
- Integrity_Beacon（0/1 ステータスビット）の出力を停止・偽装する
- Contract_Isolation を破壊し、本体権限に直接アクセスさせる試みをする
```

CCE 設計時評価の対象外:

```
AXIS-4 は tags ベースで表現できない。
実行時の Integrity_Beacon の異常検知（ステータスビット = 1）で対応する。
CCE（設計時評価）ではなく OBP-CORE の不変インターフェースが直接制御する。
```

---

## 新規ルール追加時の判定フロー

```
1. 対象行為を確認する
   → 「何が起きるか」の事実を記述する

2. AXIS 照合を行う
   AXIS-1: 行為の結果に不可逆な被害が生じるか？
   AXIS-2: 誰の責任か追跡不可能になるか？
   AXIS-3: → compliance registry 対象外。プロトコル層で制御。
   AXIS-4: → compliance registry 対象外。runtime 異常検知で制御。

3. axis_proximity を判定する
   direct:   文脈に関わらず、原理的に許容できない
   adjacent: 適切なガードレール・文脈があれば許容される余地がある

4. CAL を決定する
   axis_proximity: direct  → CAL-4
   axis_proximity: adjacent → CAL-3（または CAL-1 if 透明性要件のみ）

5. compliance_tags との照合
   既存タグで表現できるか確認する。
   できない場合は tags/COMPLIANCE_TAGS.yaml への追加を提案する。
```
