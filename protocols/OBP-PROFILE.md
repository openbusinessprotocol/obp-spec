# OBP-PROFILE — Profile Protocol (Emotional / Cognitive / Maturity)

Status: Adopted (PROP-087, 2026-04-07)

---

## 0. Profile Architecture — Three-Dimensional Differentiation

OBP-PROFILE was originally designed as an "emotional profile,"
but the axes that characterize beings, content, and context are not limited to emotion.

```
OBP-PROFILE (Profile Protocol) = composite of three orthogonal dimensions

  [E] Emotional Profile (emotional dimension)
      → tone, valence, urgency of content
      → the writer's emotional state → stylistic resonance

  [K] Knowledge / Cognitive Profile (intellectual dimension)
      → being's domain expertise, abstraction level
      → content's reading difficulty, prerequisite knowledge level
      → accuracy of expressing depth of thought in language

  [M] Maturity Profile (experiential dimension)
      → being's maturity, track record, accumulated trust
      → "experience level" expressed in OBP terms
```

All three dimensions together complete "a being's profile."

---

## 1. Concept Definition

OBP-PROFILE is the protocol that expresses the "emotional, tonal, and cognitive profile"
of content, notifications, and documents as standard tags.

OBP-PROFILE originated as `annotations._ep` within OBP-DOC but became independent because:
- Emotional tags are needed across OBP-MSG / OBP-NTFY / OBP-DOC / OBP-ACT
- A formal independent specification is needed for AI tone/style control

---

## 2. OBP-PROFILE Record Structure (Three-Dimensional Integration)

```json
{
  "_obp_type": "OBP-PROFILE/1.0",

  "emotional": {
    "valence":  "positive" | "negative" | "neutral" | "mixed",
    "arousal":  0.0,
    "urgency":  0.0,
    "tone":     "formal" | "casual" | "assertive" | "empathetic" | "inspiring" | "cautionary",
    "register": "keigo" | "teineigo" | "informal" | "technical" | "narrative",
    "energy":   0.0,
    "intent":   "inform" | "persuade" | "motivate" | "warn" | "celebrate" | "console"
  },

  "cognitive": {
    "depth":         "surface" | "intermediate" | "deep" | "expert",
    "domains":       ["string"],
    "abstraction":   0.0,
    "reading_level": "elementary" | "general" | "professional" | "specialist",
    "assumed_prior": ["string"]
  },

  "maturity": {
    "level":        0.0,
    "experience_areas": ["string"],
    "track_record": "none" | "limited" | "established" | "proven",
    "confidence":   0.0
  }
}
```

### Field Explanations by Dimension

[E] Emotional Dimension

| Field | Meaning |
|-------|---------|
| `valence` | Emotional direction (positive/negative/etc.) |
| `arousal` | 0=calm / 1=excited. Content energy |
| `urgency` | 0=low priority / 1=urgent. Maps to OBP-ACT urgency |
| `tone`    | Tone style |
| `register`| Language register (honorific levels, etc.) |
| `energy`  | Enthusiasm corresponding to content resonance score |
| `intent`  | Communication intent |

[K] Cognitive Dimension

| Field | Meaning |
|-------|---------|
| `depth`   | Depth of thought. surface=shallow / expert=specialist |
| `domains` | Related knowledge domains ("marketing", "finance", etc.) |
| `abstraction` | 0=concrete / 1=abstract |
| `reading_level` | Level required to comprehend the content |
| `assumed_prior` | Knowledge assumed as prerequisite |

[M] Maturity Dimension

| Field | Meaning |
|-------|---------|
| `level`   | 0=beginner / 1=expert |
| `experience_areas` | Areas of demonstrated experience |
| `track_record` | Level of accumulated track record |
| `confidence` | 0=uncertain / 1=certain. Confidence level (AI generation = certainty score) |

### Granularity Guide for Use

Not all dimensions need to be filled every time. Select based on purpose:
- Writer profile: E + K required, M optional
- Agent capability description: K + M required, E optional
- Notification priority control: `emotional.urgency` only
- Content difficulty label: `cognitive.reading_level` only

---

## 3. Embedding Patterns (Loose Coupling)

OBP-PROFILE is embedded in other protocols via `ep_ref` or inline:

```json
// Within OBP-DOC document (inline)
"annotations": {
  "_ep": { "_obp_type": "OBP-PROFILE/1.0", "valence": "positive", "urgency": 0.2, ... }
}

// Within OBP-ACT (reference)
"context": {
  "ep_ref": "obp:ep:01JRXXX"   // if urgency=0.9, change notification visual treatment, etc.
}

// Within OBP-NTFY (inline)
"ep": { "_obp_type": "OBP-PROFILE/1.0", "urgency": 0.8, "tone": "cautionary" }
```

---

## 4. Impact on Rendering

OBP-PROFILE influences the OBP-DOC Rendering Context:

```
OBP-DOC document
  + EP: { urgency: 0.9, tone: "cautionary" }
  ↓
Rendering Context determination:
  → color: warning orange (Design Token: --color-warning)
  → font weight: bold
  → banner: with warning icon

EP: { valence: "positive", arousal: 0.7, tone: "inspiring" }
  → color: accent color
  → animation: light fade-in
```

---

## 5. OBP-PROFILE in AI Generation

When AI generates content, EP is passed as a constraint:

```json
{
  "prompt": "...",
  "ep_constraints": {
    "_obp_type": "OBP-PROFILE/1.0",
    "tone": "empathetic",
    "register": "teineigo",
    "urgency": 0.3,
    "valence": "positive"
  }
}
```

This makes "same content with only tone changed" possible at the API level.

---

## 6. Relationships to Other Protocols (MECE)

```
OBP-PROFILE is a "cross-cutting embedded" protocol (orthogonal axis to all):

  OBP-DOC  ← EP attaches emotional tags (document mood)
  OBP-ACT  ← EP used in urgency determination (urgency of approval)
  OBP-MSG  ← EP defines message tone
  OBP-NTFY ← EP determines notification visual priority

OBP-PROFILE does not change the "meaning" of content. It adjusts expression and reception.
```
