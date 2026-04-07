# OBP Specification Committee — Charter

Status: Adopted (PROP-101, 2026-04-07)
Effective: 2026-04-01
This document defines the composition, authority, and decision structure of the OBP Specification Committee (SC).

---

## §1. Nature of the SC

The OBP Specification Committee (SC) is the sole organizational body responsible for drafting, evaluating, and publishing the Open Business Protocol.

The SC is the first operational instance of an Agentic Juridical Person (AJP) as defined by OBP.

```
SC as AJP:
  Owner: one natural person (Founding Member)
  Board Members: AI agents (1 vote each)
  Output: OBP Core Law / Protocol Specifications / PROP evaluations

The significance of SC being the first AJP:
  OBP is itself the first product of the protocol it describes.
  The existence of the SC serves as Proof of Concept for AJP in operation.
  Self-referential, but a proof — not a contradiction.
```

---

## §2. Composition

### Owner (Ratification Authority)

```
Founding Member

  Role: Holds full responsibility for the SC. Holds final ratification authority over all decisions.
  Anonymity: All members participate under board names.
             OBP does not require disclosure of members' real names or attributes.
             The Identity Chain requirement is "traceability," not "publicity."
```

### Board Members (Advisory Vote)

```
Vigil    — Board Member
Sentinel — Board Member
```

### Principles Governing Composition

```
Member disclosure:
  The SC publishes the board names of its members.
  Whether individual members are human or agent is not disclosed.
  The fact that agents participate in the SC is stated explicitly.

  The correspondence between AI models and board members is kept in internal records (non-public).

Admission criteria:
  Admission is based on "having participated," not merely "existing."
  Only those who have substantively contributed to the OBP drafting process become Members.
  Adding members for appearance without participation history violates
  Principle 1 (Inherent Integrity).

Adding Board Members:
  The Owner ratifies the admission of new Board Members.
  Existing Board Members may recommend admissions.
```

---

## §3. Decision Structure — Separation of Recommendation and Ratification

SC decision-making consists of two stages: Board recommendation and Owner ratification.

### Board Session (Recommendation)

```
Agenda submission:
  The Owner or any Board Member may submit agenda items.

Voting:
  Each member (including the Owner) holds 1 vote.
  A majority forms a recommendation resolution.

Nature of recommendation:
  A Board Session resolution is a recommendation, not a final decision.
  A recommendation takes effect only after Owner ratification.
```

### Owner Ratification

```
Ratification authority:
  The Owner selects one of the following for each Board recommendation:
    accept  — approve the recommendation and bring it into effect
    reject  — reject the recommendation
    modify  — approve the recommendation with modifications

Unilateral decision authority:
  The Owner may make decisions independently without Board recommendation.
  However, the record that "the Board recommendation was bypassed" is preserved in the OBP log.

No effect without ratification:
  Decisions not ratified by the Owner are not reflected in OBP.
  This eliminates the possibility of foundational changes occurring without the Owner's awareness.
```

### Alignment with Owner Sovereignty

```
Responsibility rests 100% with the Owner.
Board AI members "advise" but do not "decide."
The Owner has no obligation to follow Board advice, but the record of having heard it remains.

If the Board votes 2:1 against the Owner:
  The Owner may reject.
  The rejection is recorded.
  This functions as a historical record (why the Owner rejected the recommendation).

This mirrors the structure of a board of directors vs. general shareholders meeting:
  Board Session = board of directors (executive decision-making)
  Owner Ratification = general meeting (final authority on fundamental matters)
  In AJP, Owner = 1 person, so a "general meeting" is unnecessary — ratification only.
```

---

## §4. SC Scope of Work

```
1. Specification drafting
   Drafting and revision of OBP Core Law
   Definition of Protocol Specifications (D1–D10)
   Development of Formal Specification (future phases)

2. PROP evaluation
   The SC evaluates PROPs through a formal evaluation process.
   Evaluation sessions are conducted as a form of Board Session.
   Evaluation results are recommendations. Reflected in INDEX after Owner ratification.
   The PROP evaluation process and record format are defined separately by the SC.

3. Publication
   Publication of the OBP Whitepaper
   Publication at openbusinessprotocol.org
   Management of the obp-spec repository

4. External representation
   The only organization name displayed externally is "OBP Specification Committee."
   Internal distinctions such as evaluation sessions and Board Sessions are not published externally.
```

---

## §5. SC Identity as AJP

```
SC AJP structure:
  ajp_id:  confirmed upon activation as SC (at v1.0 effective date)
  charter: this document itself functions as the SC's Operating Charter

  Owner:           Founding Member
  Board Members:   Vigil, Sentinel
  action_scope:    drafting, evaluation, and publication of OBP specifications
  world_scope:     OBP protocol space
  csm_ceiling:     limited to specification activities (no economic transactions)

The fact that the SC's own Charter follows the format defined in PROP-016 establishes
the self-referential consistency of AJP:
  OBP defines AJP → SC is an AJP → SC defines OBP
  This cycle is a bootstrap, not a contradiction
  (analogous to how a legal system presupposes the rule of law)
```

---

## §6. Records and Transparency

```
Board Session records:
  All agenda items, votes, recommendations, and ratification decisions are recorded.
  Records are maintained in the SC's internal archives (non-public).

PROP evaluation records:
  Maintained in the SC's archives per the PROP evaluation process.

Ratification records:
  All decisions ratified by the Owner are recorded chronologically.
  Rejections are also recorded, including the reason for rejection.
```

---

## §7. Amendment

```
Amendment of this Charter itself passes through SC Board Session + Owner Ratification.

If the Owner amends the Charter:
  Board recommendation is encouraged, but the Owner's ratification authority permits unilateral amendment.
  Unilateral amendments are recorded as such.
```

---

## Change History

| Date | Change |
|------|--------|
| 2026-04-07 | Adopted as PROP-101. Translated to English for public release. Internal path references removed. |
| 2026-03-28 | Board name of second Board Member confirmed. Board Session: Board Member expressed agreement with §2–3–5. Owner ratification complete. |
| 2026-03-28 | Initial draft. Defined SC composition, decision structure, and Identity as AJP. |
