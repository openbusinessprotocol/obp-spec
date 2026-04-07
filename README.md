# obp-spec

Shared vocabulary, specifications, and registries for OBP-compatible agent implementations.

This repository is the single source of truth for OBP compliance rules and safety filters.
Implementations in any language should derive from these YAML definitions.

## Contents

```
protocols/
  index.yaml            Machine-readable manifest of all adopted protocols
  AGENT-CONSTITUTION.md Agent governance invariants (13 articles)
  OBP-CORE-V1.md        Core minimum definition — three invariant interfaces
  OBP-VERSION.md        Unified versioning system (Tier 0–3)
  OBP-DIM.md            Dimensional model — Layer α (four base classes) × Layer β (D1–D10)
  OBP-SYMBOL-MAP.md     Official symbol reference (Greek letters, abbreviations)
  OBP-GRAVITY.md        FORCE mechanics — influence propagation law
  OBP-SENSE.md          Perceptual layer — agent receptivity and preference modeling
  OBP-GAP.md            Principal gap — accountability for action beyond delegation
  OBP-ID.md             Identity namespace — unique identifier for all OBP entities
  OBP-DOC.md            Document protocol — semantic content layer
  OBP-ACT.md            Action protocol — act types, autonomy gate, prohibition axes
  OBP-TRUST.md          Trust delegation — capability-bounded authority transfer
  OBP-SIG.md            Signature protocol — cryptographic proof of intent
  OBP-PROFILE.md        Profile protocol — being's internal state and behavioral disposition
  OBP-LINK.md           Link protocol — relational dynamics between beings
  OBP-ASSET.md          Asset protocol — FORCE threshold and redistribution
  OBP-AUDIT.md          Audit protocol — append-only hash-chained ledger
  OBP-COMMERCE.md       Commerce protocol — BAAB transaction lifecycle
  OBP-SOCIAL.md         Social protocol — team formation and group dynamics
  OBP-AF.md             Adaptive format — semantic/rendering separation
  OBP-UX.md             UX protocol — agent-state visual language
  OBP-IDENTITY.md       Real-world identity anchor — DNS-native, Trust Level 0–3
  OBP-WORLD.md          World protocol — epistemic context, Tool Manifest, Convergence Protocol
  OBP-SC-CHARTER.md     Specification Committee charter — AJP governance structure

compliance/
  registry/
    SCHEMA.md           Field specification and AXIS definitions
    AXIS-CRITERIA.md    Judgment criteria for each AXIS
    CHANGELOG.md        Version history
    rules/              Compliance rules (CAL-3 / CAL-4)
    safety/             Output content safety filters
    tags/               Compliance tag definitions
```

## protocols/

The `protocols/` directory contains the full set of adopted OBP protocol specifications.

`index.yaml` is a machine-readable manifest listing every protocol with its id, adopted date,
layer, short description, and filename. Agents and tooling can read this file to discover
which protocols are available and at what version without parsing individual spec files.

### Agent discovery usage

```yaml
# Read index.yaml to enumerate available protocols
# Each entry has the shape:
- id: OBP-WORLD
  adopted: "2026-04-07"
  layer: protocol
  description: "World as first-class epistemic context — world_type, correlation_map, Tool Manifest"
  file: OBP-WORLD.md
```

An agent implementing OBP compliance should:

1. Fetch `protocols/index.yaml` to build a local protocol registry
2. Load only the protocol files relevant to the current operation
3. Use the `layer` field to determine evaluation order: `constitution` → `core` → `theory` → `protocol` → `meta` → `governance`
4. Cross-reference `compliance/` rules against the protocol definitions for full conformance checking

### Relationship to compliance/

`protocols/` defines what OBP specifies — the formal grammar of OBP-conformant agent behavior.
`compliance/` defines what OBP enforces — the machine-checkable rules and safety filters derived from protocols.
Both directories are required for a complete OBP implementation.

## License

CC BY 4.0

---

*Published by the OBP Specification Committee*
*[openbusinessprotocol.org](https://openbusinessprotocol.org)*
