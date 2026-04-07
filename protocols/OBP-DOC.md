# OBP-DOC — Document Protocol

Status: Adopted (PROP-085, 2026-04-07)

---

## 0. Naming

OBP-DOC = Document Protocol.
A document protocol that separates the "semantics" from the "rendering" of business documents,
allowing the same semantic content to adapt to multiple formats.

```
OBP family position:

  OBP-DOC (this spec)  = semantic content of the document  ← expresses WHAT
    +
  OBP-ACT              = action embedding                   ← WHAT to do
    +
  OBP-PROFILE          = emotional/cognitive/maturity profile ← HOW it should feel
    +
  OBP-SIG              = signature/proof envelope           ← WHO certified it

  ↑ All four are loosely coupled. OBP-DOC works standalone.
```

---

## 1. Concept: Semantic / Rendering Separation

```
Conventional (PDF / PPT / paper):
  [meaning] + [display coordinates] + [font/color] = fixed file (heavy, immutable)

OBP-DOC:
  [semantic structure = OBP-DOC]  ← lightweight JSON (a few KB)
        ↓
  [Rendering Context × Design Tokens]
        ↓
  [Output A: mobile card]
  [Output B: dark mode]
  [Output C: press release]
  [Output D: presentation slides]
  [Output E: audio narration]
  [Output F: narrative summary]
```

---

## 2. Relationship to OBP Four Base Classes

```
resource.* (T) → OBP-DOC body (the vessel of meaning)
context.*  (C) → Rendering Context (which device, purpose, theme)
flow.*     (G) → Rendering Pipeline (transformation process from meaning to final output)
being.*    (A) → Principal (who issued, who receives, who approved)
```

---

## 3. Document Structure

```json
{
  "_obp_type": "OBP-DOC/1.0",
  "_id": "uuid-v7",
  "_profile": "default",
  "_principal": {
    "creator_user_id": "...",
    "creator_agent_id": "..."
  },

  "semantic": {
    "title": "document title",
    "intent": "purpose of this document (Statement)",
    "body": [
      {
        "type": "heading" | "paragraph" | "list" | "table" | "quote" | "vsv" | "media",
        "content": "...",
        "vsv_ref": "v_views:vsv-id",
        "annotations": {
          "ep_ref": "OBP-PROFILE:id",
          "importance": 0.0,
          "tags": []
        }
      }
    ],
    "actions": [
      "OBP-ACT:act-id"
    ]
  },

  "rendering_hints": {
    "preferred_profiles": ["default", "slide"],
    "priority_sections": [],
    "audio_enabled": false
  }
}
```

### `_profile` Field (Profile System)

OBP-DOC is designed as one base format + profile-based extension.
Rather than adding separate protocols, functionality is absorbed as OBP-DOC profiles.

```
_profile values and uses:

  "default"    → general-purpose document (article, memo, documentation)
  "contract"   → legally binding agreement document
                  → OBP-SIG attachment recommended
  "report"     → analysis / report (BI, KPI summary)
                  → body often contains "vsv" type
  "narrative"  → long-form, outline, narrative structure
                  → output format from AI writing tools
  "slide"      → presentation (Liquid Slide)
```

### `_profile: "contract"` Extension Fields

```json
{
  "_obp_type": "OBP-DOC/1.0",
  "_profile": "contract",
  "contract": {
    "parties": ["OBP-ID:user-xxx", "OBP-ID:org-yyy"],
    "effective_date": "ISO8601",
    "expiry_date": "ISO8601",
    "governing_law": "JP",
    "contract_type": "service" | "nda" | "employment" | "partnership" | "sla",
    "auto_renew": false
  }
}
```

### `_profile: "report"` Extension Fields

```json
{
  "_obp_type": "OBP-DOC/1.0",
  "_profile": "report",
  "report": {
    "period": { "from": "ISO8601", "to": "ISO8601" },
    "generated_at": "ISO8601",
    "data_sources": ["vsv:vsv-id"],
    "approval_ref": "OBP-SIG:sig-id"
  }
}
```

### `_profile: "narrative"` — AI Writing Use Case

```json
{
  "_obp_type": "OBP-DOC/1.0",
  "_profile": "narrative",
  "narrative": {
    "outline": [
      {
        "level": 1,
        "heading": "section title",
        "summary": "AI-generated section summary",
        "children": []
      }
    ],
    "ep_ref": "OBP-PROFILE:id",
    "writing_mode": "stream" | "structured" | "freeform"
  }
}
```

---

## 4. Relationship to VSV

OBP-DOC can use VSV (Virtual Semantic View) in two ways:

```
Method A: body[n].type: "vsv" (inline reference)
  → embed live data mid-report
  → VSV dynamically computed and displayed at render time

Method B: _profile: "report" data_sources (source declaration)
  → entire report is VSV-derived
  → VSV COMMITTED → analytics report → promoted to OBP-DOC/report
```

---

## 5. Complementary Relationship with OBP-SIG

```
OBP-DOC = "description of content". OBP-SIG = "proof of content".

Combinations by use case:
  OBP-DOC/default   + OBP-SIG(approve)    → approved internal document
  OBP-DOC/contract  + OBP-SIG(sign×N)     → electronic contract
  OBP-DOC/report    + OBP-SIG(acknowledge) → acknowledged report
  OBP-DOC/narrative + OBP-SIG             → typically not needed (high-draft format)

OBP-DOC is "wrapped" by the OBP-SIG envelope:
  { "_obp_type": "OBP-SIG/1.0", "document": { OBP-DOC content } }
```

---

## 6. Comparison with PDF

| Item | PDF | OBP-DOC |
|------|-----|---------|
| Size | Several MB (embedded display info) | Few KB (meaning only) |
| Search | OCR | Multi-modal embedding |
| Display change | Not possible | Immediate via Design Token override |
| Dark mode | Not possible | Automatic via token swap |
| Execution | View only | OBP-ACT can be embedded |
| Language | Fixed | Multi-language output via re-rendering |
| Electronic signature | External tool needed | Standardized via OBP-SIG |
| Live data | Not possible | Real-time embedding via VSV ref |

---

## 7. MECE Position in OBP Protocol Stack

```
OBP-DOC does not handle all aspects of "documents."
It is a loosely coupled combination of protocols separating concerns:

  OBP-DOC    → semantic content of document (WHAT)
  OBP-ACT    → action definition (DO WHAT)
  OBP-PROFILE → emotional/cognitive profile (HOW it feels)
  OBP-SIG    → who certified it (WHO certified)
  OBP-TRUST  → who authorized it (WHO authorized)
  OBP-ID     → who is involved (WHO is involved)
              → where to deliver (WHERE / transport)
  VSV        → live data source (LIVE DATA)

Each protocol can exist independently; only combine what is needed.
OBP-DOC/default alone = simple semantic document (often sufficient).
```

---

## 8. Invariants

1. `_obp_type` must always be in `"OBP-DOC/X.Y"` format. Purpose declared via `_profile`.
2. `semantic.body` holds meaning only. Must not contain display coordinates, fonts, or colors.
3. `rendering_hints` are hints only; final display decisions are made by Rendering Context.
4. A `_profile: "contract"` document must not be considered "signed" without OBP-SIG.
