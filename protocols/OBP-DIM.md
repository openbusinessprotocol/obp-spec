# OBP-DIM — Dimensional Model

Status: Adopted (PROP-098, 2026-04-07)

---

## 1. Concept Definition

OBP-DIM is a meta-protocol that defines "the dimensional structure of the OBP description space."

```
"Dimension" in OBP =
  choices of "which axis to describe" the same business event from

Just as physical dimensions (x/y/z/t) describe "where and when something is,"
OBP dimensions specify "from which perspective and at what depth" a business event is described.
```

OBP-DIM is not an independent protocol; it describes the relational structure of all other OBP protocols.

---

## 2. Two-Layer Dimensional Structure

OBP dimensions consist of two layers with different natures.

```
┌─────────────────────────────────────────────────────────────────────┐
│  Layer α: Ontological Basis                                          │
│  = the "coordinate axes" of the business universe.                  │
│    The space itself that is the prerequisite for description.        │
│                                                                     │
│    A  being    — who (subject, will)                                 │
│    T  resource — what (assets, objects, value)                       │
│    G  flow     — how it moves (procedure, time)                      │
│    C  context  — in what situation (boundary, meaning, soul)         │
│                                                                     │
│  Physical analogy: spacetime (x, y, z, t)                           │
│  → The four bases span "business spacetime."                        │
│    All descriptions are projected onto this space.                   │
└─────────────────────────────────────────────────────────────────────┘
               ×
┌─────────────────────────────────────────────────────────────────────┐
│  Layer β: Descriptive Dimensions                                     │
│  = degrees of freedom in "how deeply" to describe                    │
│    each point in business spacetime.                                 │
│                                                                     │
│    D1  OBP-ID      — precision of "who" (Identity)                   │
│    D2  OBP-DOC     — depth of semantic content (Content)             │
│    D3  OBP-ACT     — granularity of acts (Action)                    │
│    D4  OBP-TRUST   — depth of authorization (Authority)              │
│    D5  OBP-SIG     — strength of proof (Proof)                       │
│    D6  OBP-PROFILE — internal state of subject (Subjective State)   │
│    D7  OBP-SENSE   — recipient's perception (Perception)             │
│    D8  OBP-LINK    — relational dynamics (Relation)                  │
│    D9  OBP-VALUE   — value/monetary dimension (Value) ← §6           │
│    D10 [temporal]  — temporal meta-dimension (Temporality) ← §5     │
│                                                                     │
│  Physical analogy: internal degrees of freedom (spin, charge, color) │
│  → Same point in spacetime can have different internal states.       │
└─────────────────────────────────────────────────────────────────────┘
```

Both layers evolve independently:
Layer α defines the "position" of business events; Layer β defines "properties."

---

## 3. Correlations and Differences with the Physical World

### Correlated Structures

```
Physical universe              OBP business universe
─────────────────────          ─────────────────────────
Spacetime (x,y,z,t)    ↔      Four base classes A × T × G × C
Fields (electromagnetic)↔      Layer β descriptive dimensions
Internal DoF (spin)    ↔      OBP-PROFILE (being's internal state)
Gravity, waves         ↔      OBP-LINK (orbital, wave, field)
Quantum superposition  ↔      Undecided state before SIG (§0 probabilistic layer)
Wave interference      ↔      OBP-LINK.interference (constructive/destructive)
Wave collapse by observation↔ State fixed by OBP-SIG (binding to the past)
```

### Essential Differences

```
Physical dimensions: geometric, continuous, describing place and time
OBP dimensions:      semantic, discrete, specifying "what to describe and how"

In physics, the observer does not affect dimensions (except relativistic inertial frames)
In OBP, "who describes it (being)" affects the description content
  → OBP-PROFILE (state of the subject) becomes the "refractive index" of description
  → The same event produces different OBP-DOC content with different PROFILEs

This is OBP's fundamental choice to "formally adopt subjectivity as a dimension."
```

---

## 4. Description Space and Dimensionality

```
Dimensionality (d) = number of Layer β dimensions applied (0–10)

d=1  : OBP-ID only       → minimum record of "someone exists"
d=2  : + OBP-ACT         → factual record of "someone did something"
d=3  : + OBP-DOC         → carries meaning of "what was done"
d=4  : + OBP-TRUST + SIG → act "permitted and proven by whom"
────── Up to here: operational description (what happened) ──────────────
d=5  : + OBP-PROFILE     → carried out in "what internal state"
d=6  : + OBP-SENSE       → "how it reached the recipient"
d=7  : + OBP-LINK        → "how it rippled through relationships"
────── From here: relational description (how it was meant) ─────────────
d=8  : + OBP-VALUE       → "what value it holds"
d=9  : + [temporal]      → "from when, until when, what happens next"
d=10 : all dimensions    → record that an agent can "fully understand"
```

### Presentation to Humans

Use `d=n` numerically only in technical contexts. For humans, enumerate active dimensions:

```
Bad: "dim.4 record"
Good: "approved and signed contract document" (DOC + TRUST + SIG active)

Bad: "dim.7 event"
Good: "event recorded including emotional state, perception, and relationship change" (PROFILE + SENSE + LINK)
```

Dimensionality (d) is an indicator of agent reasoning capability:
- d ≤ 3: pattern matching (routine processing)
- d 4-6: contextual judgment (with TRUST/SIG/PROFILE)
- d 7+: semantic understanding (reasoning via SENSE/LINK/VALUE)

---

## 5. Temporal Dimension

In OBP, time plays two different roles.

```
Role 1: Timestamp (coordinate accompanying all records)
  → _meta.created_at / updated_at / expires_at
  → Handled by Layer α's G (flow)

Role 2: Temporal state (semantics of past/present/future)
  → "Is this record still valid?" "Will it change in the future?"
  → Currently not formally defined in OBP
```

### Three Temporal Axes

```
Past (Past)   = immutable facts fixed by OBP-SIG
               "This happened" ← cannot be tampered with

Present       = currently valid state
               "This is still true"
               Validity bounded by valid_from / valid_until

Future        = three types of future:
  planned     → OBP-DOC/plan (intended future)
  authorized  → OBP-TRUST (possibility space of permitted acts)
  projected   → trajectory estimated from OBP-LINK.history.trend
```

### Temporal Meta-Dimension (D10) Design Policy

Temporal dimension is designed not as an independent protocol but as an annotation that can be added to other records:

```json
"_temporal": {
  "valid_from":  "ISO8601",
  "valid_until": "ISO8601",
  "state":       "past | present | future | projected",
  "causal_refs": ["event:prev_id"],
  "version":     "1",
  "supersedes":  "record:old_id"
}
```

This allows OBP-DOC, OBP-TRUST, and OBP-VALUE to express "valid from when."

---

## 6. Value/Monetary Dimension (OBP-VALUE)

Value potentially exists in all business events, but OBP currently has no formal dimension for it.

### Four Forms of Value

```
Exchange Value      = tradable quantity in the market
                      → Most objective. Expressible in currency, points, time

Book Value          = accounting record value
                      → Made immutable combined with OBP-SIG (e.g., at fiscal close)

Perceived Value     = value felt by recipient
                      → Linked to OBP-SENSE.preference. Subjective.

Relational Value    = value generated by relationships
                      → High OBP-LINK.resonance relationships amplify value
```

### OBP-VALUE Structure (Proto-definition)

```json
{
  "_obp_type": "OBP-VALUE/1.0",
  "subject_ref":   "OBP-ID ref (resource or being or event)",
  "amount":        0.0,
  "unit":          "JPY | USD | EUR | hours | credits | points | ...",
  "value_type":    "exchange | book | perceived | relational",
  "valuation":     "market | cost | dcf | arbitrary | transactional",
  "confidence":    0.0,
  "as_of":         "ISO8601",
  "valid_until":   "ISO8601",
  "currency_pair": null
}
```

### P&L and Balance Sheet in OBP

```
Balance Sheet (BS) = aggregate of resource.T value at a specific point T
  = OBP-DOC/report { profile: "report" }
  + body[n] as structured data aggregating OBP-VALUE[]
  → Assets      = sum of resource with OBP-VALUE.amount > 0
  → Liabilities = sum of resource with OBP-VALUE.amount < 0 (obligations, debt)
  → Equity      = Assets − Liabilities (derived value)

P&L (Income Statement) = aggregated value change of flow.G events over period [T1, T2]
  = OBP-DOC/report
  + body[n] as period aggregate of OBP-ACT/payment × OBP-VALUE
  → Revenue = sum of OBP-VALUE of received flows
  → Expense = sum of OBP-VALUE of payment flows
  → Net     = Revenue − Expense

BS and P&L are "financial projections" of OBP description space.
Views extracting only the financial dimension (OBP-VALUE) from full-dimension records.
```

---

## 7. Business Use Case Coverage

### Areas Covered by D1–D8 (8 dimensions)

| Category | Coverage | Primary Protocols |
|----------|----------|-------------------|
| Documents, agreements, contracts | 95% | DOC + TRUST + SIG |
| Tasks, workflows | 90% | ACT + TRUST + ID |
| Human-agent relationships | 85% | LINK + PROFILE + SENSE |
| Communication | 80% | DOC + PROFILE + SENSE |
| Authentication, authorization | 95% | ID + TRUST + SIG |

### Remaining Gaps and Responses

```
Gap 1: Monetary/asset value (most critical)
  Current: dispersed, no unified protocol
  Solution: formal adoption of OBP-VALUE (D9)
  Impact: all transactions, accounting, billing, credit, pricing

Gap 2: Temporal formalization (medium)
  Current: timestamps exist per record. No formal definition of validity period, causality.
  Solution: protocolization of _temporal annotation (D10)
  Impact: contract validity periods, version management, audit tracking, forecasting

Gap 3: Quantity/measurement (small)
  Current: ad-hoc per resource field
  Solution: OBP-VALUE unit field can encompass time/count/volume
  Impact: inventory, labor management, resource consumption

Gap 4: Physical location (varies by business type)
  Current: partial coverage via context(C)
  Solution: _v.location namespace sufficient (not needed for digital businesses)
  Impact: logistics, physical stores, field services
```

### Minimum Additional Protocols for 90% Coverage

```
Required:
  OBP-VALUE (D9)        ← foundation for all P&L/BS, transactions, pricing
  _temporal annotation  ← contract validity period, version, causality

Optional (business-type dependent):
  _v.location           ← only needed for physical businesses
  OBP-MSG (future)      ← messaging (currently substitutable with DOC + ACT)
```

D1–D9 + _temporal = OBP coverage 90%+

---

## 8. Dimensional Invariants

1. Dimensions are orthogonal. D6 (PROFILE) and D7 (SENSE) can vary independently.
   A subject's emotional state and recipient's perception are separate things (MUST NOT confuse).

2. Adding dimensions is optional. A d=2 record and a d=10 record are equally valid OBP records.
   More dimensions does not mean "better" — it means "higher precision."

3. Layer α (four base classes) is irreducible. A description omitting any base class
   is not an OBP record but mere data.

4. OBP-VALUE (D9) is the quantification of resource.T, not an evaluation of being.A.
   Expressing "the value of a person" with OBP-VALUE is prohibited (MUST NOT).

5. High-dimensional records cannot be reversibly projected to low-dimension.
   Projection from d=10 → d=2 loses information. Reverse transformation is impossible.

---

## 9. Relationships to Other Protocols

```
OBP-DIM is a meta-protocol. It references all other OBP-XX as dimensions:

  D1: OBP-ID     D2: OBP-DOC    D3: OBP-ACT
  D4: OBP-TRUST  D5: OBP-SIG    D6: OBP-PROFILE
  D7: OBP-SENSE  D8: OBP-LINK   D9: OBP-VALUE
  D10: _temporal (considering future formalization as OBP-CHRONO)

OBP-DIM defines the "description depth" and "coverage" of OBP records.
The judgment of which dimensions to apply and when is the responsibility of the World Filter (implementing platform).
```
