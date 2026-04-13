# OBP Taxonomy

OBP Taxonomy defines the ontological vocabulary for classifying all concepts in an OBP-compliant system.

## Structure

```
taxonomy/
  genera.yaml    — The four root genera (immutable)
  species.yaml   — Canonical species within each genus
  variants.yaml  — Sub-types of species (community-proposable)
  README.md      — This file. Extension namespace rules.
```

## Notation

| Level | Format | Example |
|-------|--------|---------|
| Genus | `genus` | `flow` |
| Species | `genus.species` | `flow.task` |
| Variant | `genus.species.variant` | `flow.task.recurring` |
| Extension | `genus.ext.{provider}.{term}` | `flow.ext.flapbase.sprint_goal` |

## Extension Namespace

Implementors may define custom species using the `ext` namespace without OBP committee approval.

### Rules

- Core namespace (`genus.species`, `genus.species.variant`) is defined in this repository. Changes require a formal PROP.
- `ext.{provider}` and below are owned by the implementor. No approval required.
- When exchanging data between different implementations, `ext.*` values are treated as **implementation-specific**. The receiving side makes no semantic guarantee.
- To propose a custom species for promotion to core, submit a PROP to the OBP Specification Committee.

### Examples

```
flow.ext.flapbase.sprint_goal      Flapbase-specific sprint goal
resource.ext.flapbase.canvas       Flapbase canvas object
context.ext.acme.sales_territory   ACME Corp sales territory definition
```

## Core Promotion Process

1. Implementor uses `genus.ext.{provider}.{term}` in production
2. Evidence of cross-platform usefulness is gathered
3. PROP submitted to OBP Specification Committee
4. If adopted, the term is added to `species.yaml` or `variants.yaml` as a core entry
