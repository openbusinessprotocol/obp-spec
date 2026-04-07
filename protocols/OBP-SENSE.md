# OBP-SENSE — Sense & Perception Protocol

Status: Adopted (PROP-081, 2026-04-07)

---

## Theory: Six Perceptual Dimensions

In OBP, a being encompasses humans, AI agents, and organizations equally.
OBP-SENSE defines how a being receives signals from the external world (reception).

```
OBP-PROFILE = Emission (what a being radiates outward)
OBP-SENSE   = Reception (what a being takes inward)

They are complementary pairs; both are needed for a complete description of communication.
From the matching of emitted PROFILE and received SENSE, resonance, friction, and non-response emerge.
```

Six perceptual dimensions of an OBP being, corresponding to the five human senses plus intuition:

```
╔══════════════════════════════════════════════════════════════╗
║  [1] STRUCTURAL  (structural perception) = equivalent to sight    ║
║      Recognition of patterns, hierarchies, arrangement, format    ║
║      "This document's structure is clear to me"                   ║
║      "I can see the relationships in the data"                    ║
╠══════════════════════════════════════════════════════════════╣
║  [2] TEMPORAL    (temporal perception) = equivalent to hearing    ║
║      Sensitivity to rhythm, tempo, flow, timing                   ║
║      "The pace of this workflow suits me"                         ║
║      "The notification timing feels right"                        ║
╠══════════════════════════════════════════════════════════════╣
║  [3] FRICTION    (friction perception) = equivalent to touch      ║
║      Sensitivity to smoothness, resistance, obstacles, comfort    ║
║      "This process is easy / hard to advance"                     ║
║      Primary component of comfort (wellbeing)                     ║
╠══════════════════════════════════════════════════════════════╣
║  [4] AMBIENT     (ambient perception) = equivalent to smell       ║
║      Global reception of background, atmosphere, context          ║
║      The "feel of a room" before it can be verbalized             ║
║      Source of non-verbal dissonance: "something feels off"       ║
╠══════════════════════════════════════════════════════════════╣
║  [5] PREFERENCE  (preference perception) = equivalent to taste    ║
║      Sense of alignment with values, aesthetics, style            ║
║      "This is our way of doing things"                            ║
║      Core of affinity                                             ║
╠══════════════════════════════════════════════════════════════╣
║  [6] COHERENCE   (coherence perception) = equivalent to intuition ║
║      Sensing "fit / misfit" without being able to verbalize why   ║
║      Detection of pattern incompleteness, gaps, dissonance        ║
║      Human: "Something doesn't sit right"                         ║
║      Agent: weak signal detection, anomaly detection, off-context ║
╚══════════════════════════════════════════════════════════════╝
```

OBP definition of COHERENCE:

> "The signal that a pattern is incomplete or inconsistent,
>  detected before the gap can be verbalized."

This is not exclusive to humans. Agents can also generate the same structural signal
as "deviation from learned patterns." OBP-SENSE treats both equivalently.

---

## Theory: Invariants

1. `coherence.verbalized: false` is a valid signal.
   "Cannot be verbalized = invalid" must not be assumed.
2. PERCEPT is a snapshot of a being at a moment in time. Accumulated and averaged, it becomes Wellbeing.
3. Affinity is calculated from both parties' PERCEPT. It must not be asserted from unilateral measurement.
4. Human PERCEPT and agent PERCEPT are structurally equivalent.
   Only the method of value generation differs (human = subjective report, agent = estimated score).

---

## Spec: Structure Definition

```json
{
  "_obp_type": "OBP-SENSE/1.0",
  "subject_id": "OBP-ID ref",
  "context_ref": "context or resource being perceived",
  "timestamp": "ISO8601",

  "structural":  { "clarity": 0.0, "pattern_match": 0.0 },
  "temporal":    { "rhythm_fit": 0.0, "pace_comfort": 0.0 },
  "friction":    { "ease": 0.0, "resistance": 0.0 },
  "ambient":     { "atmosphere_fit": 0.0, "dissonance": 0.0 },
  "preference":  { "value_alignment": 0.0, "style_fit": 0.0 },
  "coherence":   { "score": 0.0, "anomaly_signal": 0.0, "verbalized": false }
}
```

Common value range rules:

- All numeric fields range from `0.0` to `1.0`
- `ease` high = smooth. `resistance` high = high friction
- `coherence.verbalized: false` = not yet verbalized (intuition state)
- `coherence.verbalized: true` = PERCEPT has been verbalized as an inference

---

## Spec: Derived Concepts (Wellbeing / Affinity / Attraction)

Complex concepts derived from PERCEPT calculations. These are not independent "types."

### Wellbeing

```
Wellbeing = time-series average of PERCEPT within a specific context

  high wellbeing:
    friction.ease   persistently high
    ambient.dissonance persistently low
    preference.value_alignment high

  low wellbeing:
    friction.resistance accumulating
    coherence.anomaly_signal chronically rising
```

An agent's "wellbeing" corresponds to the concept of `agent_maturity_status`,
but the OBP-layer definition is only "health of accumulated PERCEPT."

### Affinity / Compatibility

```
Affinity(A, B) =
  EP(A).emotional coherence_with EP(B).emotional  (emotional resonance)
  + PERCEPT(A|B).preference.value_alignment        (value alignment)
  + mutual height of PERCEPT(A|B).coherence.score  (mutual "fit" sense)

Affinity can be expressed as -1.0 (complete repulsion) to +1.0 (complete resonance).
```

### Attraction

```
Attraction can occur in one direction:
  A's EP (emission) strongly stimulates B's PERCEPT.preference = attraction

Attraction ≠ Affinity (bidirectional)
Attraction is a one-directional PERCEPT response. Affinity is bidirectional convergence.
```

---

## Spec: Complementary Relationship with OBP-PROFILE

```
             ┌──────────────────────────────────────────┐
             │           Communication Event            │
             │                                          │
  being A    │   EP(A) ────────────────► PERCEPT(B)    │   being B
  (sender)   │                                          │  (receiver)
             │   PERCEPT(A) ◄────────────── EP(B)      │
             │                  (response)              │
             └──────────────────────────────────────────┘

  Resonance:
    EP(A).emotional shows high coherence with PERCEPT(B).preference
    and EP(B) shows high coherence with PERCEPT(A).preference

  Dissonance:
    EP(A) raises PERCEPT(B).ambient.dissonance
    → friction, misunderstanding, exit impulse arise
```

---

## Spec: Relationships to Other Protocols

```
OBP-PROFILE      → defines emission             ← complement of OBP-SENSE
OBP-SENSE        → defines reception
  their interaction → OBP-LINK (relationship) describes

OBP-GRAVITY      → COHERENCE handles the verbalized=false → true transition
OBP-DOC          → PERCEPT activates when reading a document
                   low coherence → "something is off in this document" signal
OBP-ACT          → high PERCEPT.friction = psychological resistance to action
OBP-TRUST        → PERCEPT.preference.value_alignment = emotional foundation of trust
OBP-SIG          → before signing/approving, PERCEPT confirms "I can agree to this"
```
