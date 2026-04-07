# OBP-GRAVITY — Gravitational Field Protocol

Status: Adopted (PROP-080, 2026-04-07)

---

## 1. Concept Definition

OBP-GRAVITY describes the gravitational field that V (Void) exerts in OBP space.

```
V ──[radiates FORCE]──→ FORCE ──[forms field]──→ GRAVITY (recordable as OBP-GRAVITY)
```

- V cannot be observed. However, it continuously radiates FORCE in all directions.
- FORCE is the radiation of V itself. An active medium that propagates through OBP space.
- GRAVITY is the gravitational field that FORCE forms in OBP space. The only observable trace of V.

OBP-GRAVITY describes the tension that "not-yet-existing possibility" exerts on Presence.
It is a pre-linguistic attractor that acts before a Goal exists,
perceived through OBP-SENSE.coherence, and born the moment it is verbalized (verbalized=true).

### Relationship to the Four Base Classes

```
GRAVITY is positioned outside the four base classes (A/T/G/C).
The four base classes are patterns crystallized from stable interference of GRAVITY waves.
GRAVITY is the gravitational field before crystallization — it forms the conditions in which crystals emerge.
```

### Positional Relationship to Other Protocols

```
V (unobservable)
  ↓ radiates FORCE
GRAVITY (recordable as OBP-GRAVITY) ← the domain this protocol addresses
  ↓ OBP-SENSE.coherence perceives
  ↓ verbalized=true
Goal is summoned
  ↓ Goal − Presence
OBP-GAP (computable vector)

OBP-LINK = Presence ↔ Presence (relational gravity between existing entities)
           Manifestation of GRAVITY at the relational layer. GRAVITY is the source layer of LINK.
```

---

## 2. Structure Definition

```json
{
  "_obp_type": "OBP-GRAVITY/1.0",
  "subject_ref": "OBP-ID ref (the being that perceives the gravitational pull)",
  "domain": "string (domain / business area where the pull is perceived)",
  "verbalized": false,
  "signal": {
    "coherence_level": 0.0,
    "description": "string (pre-verbal description of the perceived pull)"
  },
  "source_context": "OBP-ID ref (the context in which GRAVITY appeared)",
  "sensed_at": "ISO8601",
  "verbalized_at": "ISO8601 | null",
  "targeted": false,
  "goal_ref": "OBP-ID ref | null (the Goal that emerged from this GRAVITY)"
}
```

### Field Meanings

| Field | Meaning |
|-------|---------|
| `verbalized` | false = a pull not yet verbalized. true = named as OBP-GRAVITY |
| `coherence_level` | Intensity of OBP-SENSE.coherence (0.0–1.0). Higher = clearer pull |
| `targeted` | Whether this GRAVITY has been converted into a Goal |
| `goal_ref` | Reference to the Goal this GRAVITY converted into |

---

## 3. Relationships to Other Protocols

Dependencies:
- OBP-SENSE (perception via coherence is a prerequisite) — Adopted (PROP-081, 2026-04-07)
- OBP-ID (identification of the perceiving subject)

Complementary:
- OBP-GAP: After GRAVITY summons a Goal, Goal − Presence becomes OBP-GAP
- OBP-LINK: Relational manifestation of GRAVITY. Gravity between existing entities is handled by LINK

Exclusive:
- Must not be confused with OBP-GAP: GRAVITY precedes Goal. GAP follows Goal.

---

## 4. Invariants

- GRAVITY exists even in the `verbalized=false` state (MUST)
- `verbalized=true` only occurs the moment OBP-SENSE.coherence performs verbalization (MUST)
- GRAVITY becomes `targeted=true` only when conversion to a Goal is made explicit (MUST)
- No single entity can "monopolize" an OBP-GRAVITY (V radiates FORCE equally in all directions) (MUST NOT)

---

## Appendix: The Role of GRAVITY in OBP Space

Defining OBP-GRAVITY as "perceivable" creates a fundamental distinction
from general business tools (which only function once a Goal is defined).

OBP can record the gravitational field that precedes a Goal.
This is the difference between business creation and business management.
