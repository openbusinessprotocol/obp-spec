# OBP-LINK — Link & Relation Protocol

Status: Adopted (PROP-091, 2026-04-07)

---

## 1. Concept Definition

OBP-LINK is the protocol that describes the dynamics of relationships
between two or more beings, resources, and contexts.

```
OBP-SENSE = the internal state of how a being "perceives" something
OBP-LINK   = the external structure of how multiple beings/entities "relate to each other"

OBP-LINK is what crystallizes from the interaction of SENSE.
"Something emerged between two beings" is described by OBP-LINK.
```

OBP-LINK integrates three concepts:

```
1. Complementary Pair Model (being↔resource / flow↔context)
   → coupling strength and direction between pairs

2. Dynamic Equilibrium
   → vectors of attraction and repulsion arising within a relationship

3. Fluctuation
   → the wave-like nature of relationships (resonance, interference, decay)
```

---

## 2. Orbital Model

In OBP, all beings are orbiting some "gravitational center."

```
Gravitational Center:
  = the central attractor of goal / value / context
  = what defines "why am I here"

Orbiting Entity:
  = being / resource / flow

Orbit Types:
  bound     : strong mutual gravity. Continuous relationship (deep partnership)
  resonant  : synchronized orbit around a common gravitational center (resonant relationship)
  passing   : temporary intersection (one-off transaction, contact)
  collision : orbital collision (competition of values, resources)
  escape    : departure from gravitational field (end of relationship, independence)
```

```
       ★ Goal/Value (gravitational center)
      /    \
     /      \
 being A    being B
(orbit A)  (orbit B)

  resonant: A and B synchronized orbit around the same ★
  collision: orbits of A and B intersect and interfere
  escape:    A or B escapes the gravitational field of ★
```

"Escape Velocity" — OBP definition:
> The sustained energy a being needs to break free from
> the gravitational pull of the current orbital system (organization, platform, relationship).

---

## 3. Wave Model

A being emits signals (EP) as "waves."
When two waves meet, interference is generated.

```
Constructive Interference = Resonance
  Wave(A) and Wave(B) match in phase and frequency
  → amplitude increases = amplification of influence, activation of co-creation
  → PERCEPT(A|B).coherence is high

Destructive Interference = Cancellation / Friction
  Wave(A) and Wave(B) are in opposite phase
  → amplitude decreases = energy drain, friction
  → PERCEPT(A|B).friction.resistance is high

Non-interference
  Wavelengths are completely different, no interference = indifference, no contact
  → PERCEPT's ambient is low (not even sensed)
```

OBP meaning of Fluctuation:
> In OBP, a human is defined as "a being that continues to fluctuate."
> In OBP-LINK, this fluctuation is expressed as "a slight shift in phase."
> A completely fixed phase means death (stillness).
> Fluctuation is what allows a being to find new resonance partners.

---

## 4. OBP-LINK Structure

```json
{
  "_obp_type": "OBP-LINK/1.0",
  "id": "rel:uuid-v7",
  "subject": "OBP-ID ref",
  "object": "OBP-ID ref | context_ref | resource_ref",
  "relation_type": "orbital" | "wave" | "field",

  "orbital": {
    "orbit_class": "bound" | "resonant" | "passing" | "collision" | "escape",
    "center_ref": "goal-id or context-id",
    "sync_level": 0.0,
    "orbit_direction": "co-orbital" | "counter-orbital"
  },

  "wave": {
    "resonance":    -1.0,
    "influence":    { "direction": "inward" | "outward" | "mutual", "intensity": 0.0 },
    "interference": "constructive" | "destructive" | "none",
    "phase_delta":  0.0
  },

  "field": {
    "attraction":  0.0,
    "repulsion":   0.0,
    "net_force":   0.0,
    "field_notes": "..."
  },

  "history": {
    "first_contact": "ISO8601",
    "peak_resonance": 0.0,
    "trend": "growing" | "stable" | "decaying" | "ended"
  }
}
```

---

## 5. Special Relationship Types

### Attraction

```
Attraction can be one-directional:
  A has strong attraction to B but B does not = asymmetric attraction

Source of attraction (intersection of OBP-PROFILE × OBP-SENSE):
  EP(B).emotional strongly matches with the attractor A's PERCEPT.preference
  → "I like the way this person/agent works" type of attraction
```

### Repulsion

```
Repulsion ≠ Indifference
Repulsion is the state of "perceived but values/direction are opposite":
  wave.interference: "destructive"
  wave.resonance: near -1.0
  PERCEPT(A|B).friction.resistance high

Repulsion can be creative: the tension between opposing forces is a dynamic engine for change.
```

### Compatibility

```
Compatibility = sustained wave compatibility of a relationship

  compatibility(A, B) =
    avg( wave.resonance over time )
    × preference_alignment
    × orbit_sync_level

  high compatibility:
    → resonant orbit, constructive interference continues
    → high collaboration efficiency, creativity

  low compatibility:
    → destructive interference repeats
    → energy drain, friction accumulation
    → not always bad (opposition may generate innovation)
```

### Gravitational Wave

```
Gravitational wave = a ripple effect where the state change of a strong being (high influence)
                     distorts the orbit of surrounding beings

OBP examples:
  - Leader's decision change → orbital realignment of all team members' tasks
  - Primary agent's capability change → execution orbits of all dependent workflows change
  - External market change (sudden context shift) → goal gravity of entire workspace moves

Gravitational waves are recorded in OBP-AUDIT as "field events."
```

---

## 6. Relationship to Four Base Classes

```
OBP-LINK is born at the "intersection" of the four base classes:

  being (A) × being (A)  = relationship (between beings' orbits)
  being (A) × resource (T) = ownership/dependency relationship
  being (A) × context (C)  = belonging/field attraction
  flow (G)  × context (C)  = execution orbit

OBP-LINK is the dynamic version of "effect.*".
If effect.* is "record of change (past),"
OBP-LINK is "the relationship that generates change (present progressive)."
```

---

## 7. Relationships to Other Protocols (MECE)

```
OBP-PROFILE → the signal a single being emits (individual)
OBP-SENSE   → the signal a single being receives (individual)
OBP-LINK    → the dynamics between two or more entities (relationship) ← this spec

The three form a trinity:
  EP (emission) + PERCEPT (reception) → interaction → OBP-LINK (description of relationship)

OBP-TRUST  → delegation relationship (sub-category of OBP-LINK conceptually,
              but independent spec specialized for autonomy/authorization)
OBP-SIG    → proof of specific agreement event (point-in-time snapshot of OBP-LINK)
```

---

## 8. Invariants

1. OBP-LINK describes relationships bidirectionally in principle.
   "A's view of B" and "B's view of A" can be separate OBP-LINK entries.
2. `orbit_class: "collision"` is not a negative evaluation.
   Collision (opposition, competition) is also a legitimate relationship mode in OBP.
3. A relationship with no fluctuation (phase_delta = 0) is a state of stillness,
   which in OBP space means the death (or fusion) of a relationship.
4. Gravitational waves propagate. When recording a being's change,
   tracking its impact on surrounding OBP-LINKs is SHOULD.
