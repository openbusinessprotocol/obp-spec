# OBP-GAP — Gap Protocol

Status: Adopted (PROP-083, 2026-04-07)

---

## 1. Concept Definition

OBP-GAP describes a computable vector calculated as the difference between Goal and Presence.

```
OBP-GAP = Goal (intended future state) − Presence (current A/T/G/C snapshot)
```

Equivalent to conventional "gap analysis" in business.
A post-linguistic, computable concept that can only be measured once a Goal exists.

### Fundamental Distinction from OBP-GRAVITY

```
OBP-GRAVITY = Before Goal. V's gravitational field. Pre-linguistic. Known only through perception.
OBP-GAP     = After Goal. The vector of Goal − Presence. Computable. Drives execution.

GRAVITY summons a Goal, and the moment the Goal is born, GAP becomes measurable.
```

Do not confuse: "The sense of lack before a Goal is defined" is OBP-GRAVITY, not OBP-GAP.

### Relationship to the Four Base Classes

```
Presence = current A/T/G/C state snapshot (actual state)
Goal     = intended future A/T/G/C state (verbalization of potential state)
GAP      = difference vector from Presence to Goal
           per-base-class difference: ΔA / ΔT / ΔG / ΔC
```

---

## 2. Structure Definition

```json
{
  "_obp_type": "OBP-GAP/1.0",
  "subject_ref": "OBP-ID ref (the being or context holding the Gap)",
  "goal_ref": "OBP-ID ref (reference to the Goal)",
  "presence_snapshot_ref": "OBP-ID ref (reference to the current state)",
  "delta": {
    "being":    { "description": "string", "magnitude": 0.0 },
    "resource": { "description": "string", "magnitude": 0.0 },
    "flow":     { "description": "string", "magnitude": 0.0 },
    "context":  { "description": "string", "magnitude": 0.0 }
  },
  "priority": "critical | high | medium | low",
  "status": "open | in_progress | closed | deferred",
  "gravity_ref": "OBP-ID ref | null (back-reference to the GRAVITY that summoned this Gap)",
  "created_at": "ISO8601",
  "closed_at": "ISO8601 | null"
}
```

### Field Meanings

| Field | Meaning |
|-------|---------|
| `delta` | Difference description per base class. magnitude is relative size |
| `gravity_ref` | Back-reference to the GRAVITY that summoned this GAP (optional) |
| `status` | open = unaddressed, in_progress = being worked on, closed = resolved |

---

## 3. Relationships to Other Protocols

Dependencies:
- OBP-ID (references to Goal and Presence)
- Goal exists as `_v.goal` or equivalent record

Complementary:
- OBP-GRAVITY: Upstream of GAP. GRAVITY produces a Goal, Goal produces a GAP
- OBP-ACT: Action descriptions that fill the GAP
- OBP-SIG: Proof of GAP resolution

Exclusive:
- Must not be confused with OBP-GRAVITY (see above)

---

## 4. Invariants

- GAP cannot be defined without a Goal (MUST)
- When all base classes in `delta` are zero, GAP is resolved (`status: closed`) (MUST)
- GAP is recalculated whenever Presence changes (SHOULD)
- Goal and Presence must belong to the same ontological level (A/T/G/C) (MUST)
