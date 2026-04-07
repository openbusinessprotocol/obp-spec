# OBP-AF — Adaptive Format

Status: Adopted (PROP-094, 2026-04-07)
Version notation rules: OBP-VERSION.md §3

---

## 1. Concept Definition — Complete Separation of Meaning and Rendering

OBP-AF is the format specification that fundamentally separates semantics (meaning) from rendering.

```
The conventional problem:
  "This document is a PDF" = meaning and rendering are inseparably bound
  Converting information in a PDF to a mobile app, slides, audio, or social post
  requires "rewriting" as a human cost

OBP-AF's solution:
  Semantic Container = belongs to resource.*
  Rendering Context  = belongs to context.*
  Explicitly separating these two produces "polymorphic output from identical meaning"
```

Correspondence to OBP four base classes:

```
resource.* (T) — WHAT:
  The "semantic content" itself of the OBP-AF document.
  Holds what is written (structure, text, media references, relationships).
  Completely independent of how it is rendered.

context.* (C) — WHERE/WHY:
  Rendering context.
  Holds "where it is displayed" and "why output in this format."
  Multiple contexts can be applied to the same resource meaning.
```

---

## 2. OBP-AF Data Structure

An OBP-AF document is a semantic container belonging to `resource.*`,
defined by the following structure.

```typescript
type OBP_AF_NarrativeType =
  | "deductive"      // Body & Message type: claim → basis. Conclusion first. Proposals, approval requests
  | "diagnostic"     // Problem-raising type: problem → cause analysis → solution. Why-first discussion
  | "enumerative"    // Listing type: parallel enumeration. Comparisons, options, summary lists
  | "sequential"     // Roadmap type: chronological flow. Plans, phases, schedule presentation
  | "narrative_arc"; // Story type: situation → turning point → resolution. Case studies, user stories

interface OBP_AF_Document {
  // OBP base fields
  _meta: {
    obp_version: string;           // OBP protocol version. Format per OBP-VERSION.md §6 (e.g., "1.0")
    semantic_type: "obp.af.doc";   // OBP-AF document type identifier
    v_version: number;
    creator_id: string;            // OBP-ID (creator)
    created_at: string;            // ISO8601 derived from UUIDv7
  };

  // Semantic content (resource.* layer)
  content: {
    title: string;
    abstract?: string;             // Abstract (for surface-level reading)
    narrative_type?: OBP_AF_NarrativeType;  // Logical structure axis (optional, PROP-005)
    body: OBP_AF_Block[];          // Block sequence (described below)
    semantic_annotations?: {
      emotional_indicators?: EmotionalIndicator[];  // Emotional/tone information
      keywords?: string[];
      entities?: OBP_ID_Reference[];
    };
  };

  // Knowledge relationships
  relations?: {
    derived_from?: string[];       // knowledge record IDs
    extends?: string[];
    depends_on?: string[];
  };

  // Rendering hints (reference to context.* layer)
  rendering_hints?: {
    preferred_contexts?: OBP_AF_Context_Type[];
    design_token_overrides?: Record<string, string>;
  };
}
```

### Block Type (OBP_AF_Block)

OBP-AF content is structured as a sequence of semantic blocks.
Each block has "what it means" and does not have "how to display it."

```typescript
type OBP_AF_Block =
  | { type: "heading";    level: 1|2|3; text: string }
  | { type: "paragraph";  text: string; emphasis?: "normal"|"key"|"quote" }
  | { type: "list";       items: string[]; ordered: boolean }
  | { type: "data_point"; label: string; value: string|number; unit?: string }
  | { type: "media_ref";  obp_id: string; alt: string; caption?: string }
  | { type: "code";       language: string; body: string }
  | { type: "separator";  semantic?: "section_end"|"context_shift" }
  | { type: "callout";    tone: "info"|"warning"|"success"|"key_insight"; text: string }
```

---

## 3. Rendering Context (context.* Layer)

Output variations of OBP-AF are determined by Rendering Context.
Applying multiple Rendering Contexts to the same OBP-AF document generates polymorphic output.

```typescript
type OBP_AF_Context_Type =
  | "screen.default"       // PC browser standard display
  | "screen.dark"          // Dark mode
  | "mobile.card"          // Mobile card type (portrait scroll optimized)
  | "presentation.slides"  // Slide deck
  | "document.press"       // Press release format (official document)
  | "audio.narration"      // Text conversion for audio narration
  | "social.post"          // Social media post summary
  | "data.structured"      // Machine-processable JSON/CSV output
  | "print.report"         // Print report
```

### Example of Polymorphic Output

```
From the same OBP-AF document "Q1 2026 Business Progress Report":

screen.default    → Normal web page (headings, paragraphs, graphs)
screen.dark       → Same structure displayed in dark mode palette
mobile.card       → Key metrics displayed in large cards with portrait scroll
presentation.slides → Auto slide generation (5-10 slides)
document.press    → Press release format (headline, body, contact)
audio.narration   → Text optimized with connectives and pauses for narration
social.post       → 280-character post + hashtag suggestions
data.structured   → Numeric data output as machine-processable CSV/JSON
```

Important: In any output, the OBP-AF document's "semantic container" does not change.
Only the rendering method (context) changes.

---

## 4. Design Tokens for Polymorphic Realization

OBP-AF treats design tokens as components of the context.* layer.
This maintains structural consistency following OBP-AF conventions
while allowing visual differences per World Filter implementation.

### Standard Design Token Definition

```typescript
interface OBP_AF_DesignTokens {
  // Typography
  typography: {
    heading_scale: "compact" | "standard" | "display";
    body_font:     string;   // CSS font-family
    code_font:     string;
    line_height:   number;   // 1.0 to 2.0
  };

  // Color system
  color: {
    surface:     string;   // background
    on_surface:  string;   // text
    primary:     string;   // accent
    key_insight: string;   // emphasis color for callout: key_insight
  };

  // Spacing
  spacing: {
    density: "compact" | "comfortable" | "spacious";
    slide_padding?: string;   // presentation context only
  };

  // Media
  media: {
    aspect_ratio: "16:9" | "4:3" | "1:1" | "auto";
    max_width:    string;
  };
}
```

### Token Override Method

World Filters inherit standard tokens and override only differences to express brand identity.

```json
// Example World Filter token override
{
  "color.primary": "#5B6EF8",
  "spacing.density": "comfortable",
  "typography.heading_scale": "standard"
}
```

---

## 5. Emotional Indicator — Emotional Annotation

Emotional Indicator is emotional/tone information embedded in
`semantic_annotations.emotional_indicators` of an OBP-AF document.

It functions as a "performance hint" to the rendering layer, but is preserved as part of meaning.

```typescript
interface EmotionalIndicator {
  scope: "document" | "section" | "block";
  block_ref?: string;        // reference to specific block (when scope="block")
  tone:
    | "confident"            // confidence, conviction
    | "cautious"             // caution, reservation
    | "celebratory"          // celebration, achievement
    | "urgent"               // urgency, warning
    | "reflective"           // introspection, looking back
    | "curious"              // inquiry, questioning
    | "empathetic";          // empathy, caring
  intensity: 0.0 | 0.25 | 0.5 | 0.75 | 1.0;   // 0.0=neutral, 1.0=maximum
}
```

Emotional Indicator is a performance hint only;
the rendering side (World Filter) decides whether to apply or ignore it.
Ignoring it is not a specification violation.

### Recommended Correspondence with narrative_type (SHOULD)

Guidance for agents estimating initial tone per narrative_type.
This does not force implementation. Human explicit specification always takes priority.

```
deductive      → confident (indicates certainty of assertion)
diagnostic     → cautious / curious (indicates complexity of problem)
enumerative    → confident / cautious (depends on content nature)
sequential     → confident (indicates certainty of plan)
narrative_arc  → empathetic / celebratory (indicates emotional resolution of experience/change)
```

---

## 6. Relationships to Other Protocols

```
OBP-DOC (Content)
  ↕ OBP-AF defines the implementation form of OBP-DOC as an expression specification for meaning

context.* (C)
  ↕ Rendering Context is a concrete example of context.* base class
  ↕ context.channel (PROP-007 §A) defines the "destination" of OBP-AF Documents
    channel's audience_descriptor can override rendering_hints

flow.publish (OBP-ACT)
  ↕ The act of making an OBP-AF Document reachable to context.channel
    rendering_policy can control context output per document

OBP-PROFILE
  ↕ Emotional Indicator links with the emotional dimension (E axis) of OBP-PROFILE

OBP-SENSE
  ↕ How rendering results "reach the recipient" overlaps with OBP-SENSE dimensions
```

---

## 7. context.channel — Receiving Context (PROP-007 §A Adopted)

context.channel is a named receiving context.
Owned by a single principal, it functions as the destination for OBP-AF Documents.
A channel holds recipient attributes, expected context types, and access conditions.

```typescript
interface OBP_Context_Channel {
  _type: "context.channel";
  channel_id: string;              // OBP-ID format
  owner_id: string;                // principal OBP-ID
  name: string;
  visibility: "public" | "restricted" | "private";
  audience_descriptor?: {
    expected_contexts: OBP_AF_Context_Type[];
    locale?: string;
    domain?: string;               // e.g., "technical" / "business" / "academic"
  };
  access_policy?: {
    require_authentication?: boolean;
    allowed_principals?: string[]; // for restricted / private
  };
}
```

visibility meanings:
- public — anyone can reach it
- restricted — authenticated/invited readers only
- private — owner and explicitly permitted principals only

### rendering_policy Priority (low → high)

```
OBP-AF Document's rendering_hints.preferred_contexts (lowest priority)
  ← channel's audience_descriptor.expected_contexts
    ← flow.publish's rendering_policy (highest priority)
```

### Grand Default (fallback when all fields unspecified)

When neither rendering_policy nor audience_descriptor is specified:
1. Estimate context from OBP_AF_Document.content.narrative_type (delegate to rendering agent)
2. If also unspecified: adopt channel.audience_descriptor.expected_contexts[0]
3. If also not set: `screen.default` (OBP-AF implicit default)

---

## 8. Invariants

```
Semantic Independence (MUST):
  OBP-AF document content must not depend on a specific rendering context.
  The specification guarantees that meaning is conveyed in any context.

Rendering Transparency (MUST):
  Changing Rendering Context does not change the block order or content of content.body.
  Only the display format changes.

Emotional Indicator is a suggestion (MUST NOT force):
  The rendering layer may reference Emotional Indicator,
  but is not forced to implement it. Ignoring it is not a specification violation.

Design tokens are overridable (MUST):
  World Filters can freely override standard tokens.
  However, the structural semantic block definitions of OBP-AF cannot be changed.

Backward compatibility (MUST):
  Future block type additions are absorbed by existing renderers ignoring unknown types.
  Unknown narrative_type values are treated the same as when omitted, without error.
  Breaking changes are only permitted in Major versions (v2.0).
```
