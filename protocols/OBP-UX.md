# OBP-UX — Business Interface Experience Semantics

Status: Adopted (PROP-095, 2026-04-07)
Created: 2026-03-22
Related: OBP-MASTER-MAP / OBP-AF

---

## 0. Principle of Definition Scope

> OBP "preserves the meaning of business." The "how to display" of UI/UX is the sovereignty of the World Filter.

This specification defines only the semantic vocabulary of business experience.

```
What OBP-UX defines:
  Names and type constraints for experience states (semantic state vocabulary)
  Classification of interaction structures (interaction mode taxonomy)
  Classification names for experience archetypes (persona taxonomy — names only)
  Classification system for friction (friction taxonomy)
  Semantic structure of message patterns (DQ pattern schema)

What OBP-UX does not define:
  Colors, CSS variables, HSL values
  Component implementations (React / Swift / Compose)
  Animation duration / easing values
  Pixel values, layout dimensions
  Font specifications
```

This boundary guarantees the freedom of World Filters
while maintaining semantic consistency between World Filters (Web / iOS / Slack Bot / Voice).

---

## §1. Semantic State (obp.ux.state.*)

Seven types of semantic states that need to be distinguished as business states.
UI is free to decide "how to display." OBP defines only the name of "what is happening."

### Three Layers of Recognizability (Recognition Decomposition)

"Visual recognizability" decomposes into three layers. World Filters must guarantee all three.

```
Detect (pre-attentive detection)
  → Signal distinguishing this state from others exists even in peripheral vision
  → Able to notice "something is different" before directing attention to the target

Read (attentive reading)
  → When focused, text or icon indicating the state is legible
  → Sufficient contrast and size; text information is identifiable

Understand (semantic understanding)
  → The meaning of the state (what is happening, what to do) is explicit or obvious from context
  → A first-time user can determine "is this something I need to respond to"
```

> L0 Premise (Visibility): Sufficient contrast ratio and visual size are prerequisites for Detect and Read.
> OBP does not specify WCAG values, but World Filters must comply with platform accessibility standards.

Recognizability MUST per state:

| State | Detect MUST | Read MUST | Understand MUST |
|-------|------------|-----------|----------------|
| nominal | neutral display not confused with other states | readable in normal display | "normal" is obvious |
| pending | dynamic signal exists (motion, blinking, etc.) | indicates in-progress | obvious that one should wait for completion |
| suspended | static but clearly different signal from nominal | label like "waiting" / "needs attention" | obvious that human intervention is requested |
| critical | highest priority signal (color, shape, position) | label prompting immediate action | what to do is explicit |
| success | positive signal exists | indicates completion/achievement | obvious that no further action needed |
| failure | negative signal exists | indicates failure/rejection | obvious that retry or intervention is needed |
| void | inactive signal (dimmed, strikethrough, etc.) | invalid state indicated | obvious that it cannot be operated |

```typescript
type OBP_UX_State =
  | "nominal"    // Normal, operating correctly. No intervention needed.
  | "pending"    // Awaiting processing, undecided. Agent or system is active.
  | "suspended"  // Paused. Waiting for external input (human decision / other flow completion).
  | "critical"   // Immediate action required. Auto-escalated.
  | "success"    // Completed, achieved. Intended result obtained.
  | "failure"    // Failed, rejected. Intervention or retry needed.
  | "void"       // Invalid. Exists but has no meaning (archived / soft-deleted).
```

### State Transition Constraints

```
nominal    → pending / suspended / critical / failure
pending    → nominal / suspended / success / failure
suspended  → nominal / pending / critical / failure  ← by human judgment
critical   → nominal / success / failure             ← auto decision or intervention
success    → void                                    ← archive only
failure    → pending                                 ← retry
void       → (terminal)
```

---

## §2. Interaction Mode (obp.ux.interaction.*)

```typescript
type OBP_UX_InteractionMode =
  | "traditional"  // Human intentionally executes all operations. Async is exception.
  | "agentic"      // Agent executes proactively. Human declares intent and approves/rejects.
  | "hybrid"       // traditional / agentic mixed by screen and context.
```

### UI Requirements per Mode (Constraints on World Filters)

```
MUST (required):
  traditional:
    - Visual feedback within 100ms of user operation
    - Operable elements are explicit (discoverability)
    - Can return to original state after error

  agentic:
    - Agent's current state (nominal/pending/suspended, etc.) always visible
    - Delegation scope (what is automatic, what requires confirmation) is explicit
    - Async completion notification is incorporated into design

  hybrid:
    - Always clear which mode one is currently in
    - Context is preserved when switching modes

SHOULD (recommended):
  agentic:
    - Implement obp.ux.message.* pattern (§4)
    - Visual emphasis on suspended state (prompting human intervention)

MAY (optional):
  All modes — animation, color, layout are freely determined by World Filter
```

---

## §3. Experience Archetype (obp.ux.persona.*)

Experience profiles aligned with user roles and expectations.
OBP provides only names and semantic definitions.
Specific visual representations are the World Filter's responsibility.

```typescript
type OBP_UX_Persona =
  | "fluid"    // Creative user. Values space, flow, organic rhythm.
  | "crisp"    // Analytical user. Values speed, density, precision.
  | "bold"     // Decision-maker. Values high signal, summary, clear CTA.
  | "minimal"  // Technical user. Maximum information density, no decoration, no explanation needed.
```

### Semantic Characteristics per Persona (Guidelines for World Filters)

| Persona | Rhythm | Density | Voice | Affordance |
|---------|--------|---------|-------|------------|
| fluid | Slow / Organic | Low | Encouraging | Minimal |
| crisp | Fast / Precise | High | Factual | Explicit |
| bold | Medium / Smooth | Low-Medium | Assertive | Prominent |
| minimal | Instant / Linear | Maximum | Terse | None |

Definitions of Rhythm / Density / Voice / Affordance:

```
Rhythm    = Interaction tempo. Animation speed, feedback immediacy.
Density   = Amount of information per screen. Amount of whitespace.
Voice     = Tone of empty state, error, and confirmation text.
Affordance= Explicitness of operation hints (amount of labels, presence of explanations).
```

---

## §4. Message Pattern (obp.ux.message.*)

Semantic structure for agents to convey information to humans.

```typescript
interface OBP_UX_Message {
  // Semantic content (resource.* layer)
  text: string;            // body of 1 message
  max_chunks: 3;           // maximum 3 lines per message (cognitive psychology constraint)

  // Interaction requirement (context.* layer)
  interaction: OBP_UX_MessageInteraction;
}

type OBP_UX_MessageInteraction =
  | { type: "auto";    delay_ms: number }              // automatically proceeds
  | { type: "confirm" }                                // human taps to proceed
  | { type: "yes_no";  yes: string; no: string }       // two choices
  | { type: "select";  options: string[] }             // multiple choices (2–5)
  | { type: "input";   prompt: string }                // text input
  | { type: "wait" }                                   // agent processing
  | { type: "done";    next_actions?: string[] }       // flow completed
```

### OBP Constraints

```
MUST:
  - One message contains only one topic
  - Multiple topics must always be split into multiple messages
  - "wait" state links with obp.ux.state.pending
  - "done" state links with obp.ux.state.success

SHOULD:
  - Messages stack chronologically; past messages should be scrollable

MAY:
  - Animation, typewriter effect, color freely determined by World Filter
```

---

## §5. Friction Taxonomy (obp.ux.friction.*)

Good experience = minimizing friction. OBP provides a classification system for friction.

```typescript
type OBP_UX_Friction =
  | "cognitive"    // difficulty understanding due to information overload/shortage
  | "operational"  // next operation not visible or reachable
  | "emotional"    // UI behavior unpredictable
  | "delegation"   // scope of agent delegation unclear
  | "contextual"   // "where am I now" is lost
```

World Filters are recommended to evaluate friction across these 5 categories during design review.

---

## §6. World Filter Implementation Guidelines

### Minimum Implementation Requirements (MUST for compliance)

```
1. The 7 states of obp.ux.state.* must be visually distinguishable.
   → At minimum: nominal / pending / suspended / critical / success / failure in different colors/icons

2. interaction_mode must be declared; in agentic mode,
   agent state must always be visible to the user.

3. In agentic / hybrid mode, there must be a design that explicitly eliminates obp.ux.friction.delegation.
   (User can understand what is automatic and what requires confirmation)
```

### Recommended Implementation (SHOULD)

```
4. Declare persona and adjust Rhythm / Density / Voice

5. Implement obp.ux.message.* pattern in agentic mode

6. Mapping from obp.ux.state to UI Token should be consistent
   (Same state in different colors per screen is not recommended)
```

### Free Domain (MAY — OBP does not intervene)

```
- Theme colors (hex / HSL / RGB)
- Component implementations (React / Swift / Compose / HTML)
- Animation duration / easing
- Layout, spacing, fonts
- Specific values for Persona (100ms vs 280ms is determined by World Filter)
```

---

## §7. Connection to OBP-MASTER-MAP

| OBP Base Class | Relationship to OBP-UX |
|---------------|----------------------|
| `being.*` | User and agent are the experiencing subjects. Persona describes their characteristics |
| `resource.*` | Data displayed on screen. obp.ux.state attaches state |
| `flow.*` | Interaction structure. Maps to interaction_mode |
| `context.*` | Rendering context (connected with OBP-AF). Persona and interaction_mode form context |

```
OBP-UX is born from the intersection of context.* and flow.*.
being.* experiences. resource.* is the object of experience.
```
