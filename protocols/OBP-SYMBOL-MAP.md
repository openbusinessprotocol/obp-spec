---
KEY: OBP-SYMBOL-MAP
description: Official reference for Greek letters and abbreviations used in OBP. Only confirmed definitions are listed. Symbols under consideration are not included in this file.
status: Adopted (PROP-100, 2026-04-07)
updated: 2026-04-07
obp_version: v1.0
---

# OBP Symbol Map — Symbol Master

> This file is the SSOT for only confirmed symbol definitions.
> Symbols under consideration and detailed adoption rationale are in separate discussion records.
> Collision checks are performed here before adopting a new symbol.

---

## §0. Three Classifications of Beings (Principal Type Classification)

Three types of legally and ontologically recognized parties recognized by OBP.
All principals belong to one of these.

```
Type I: Natural Person
  Base:    biological entity
  Nature:  indivisible source of responsibility. Holds rights and obligations bodily and inherently.
  OBP:     starting point of Owner / Principal. Ultimate convergence point of Owner Sovereignty.
  Note:    a prior condition existing outside OBP. OBP presupposes rather than defines it.

Type II: Juridical Person
  Base:    social contract (collective)
  Nature:  legal personality formed by agreement among multiple Type I. Responsibility is dispersed and deliberative.
  OBP:     traditional business entity. multi-principal vessel.

Type III: AJP — Agentic Juridical Person
  Base:    Type I responsibility structure + Juridical Person legal vessel + AI agent group
  Nature:  combining "unity of responsibility (from Type I)" and "scalability of execution capability (from AI)"
  OBP:     legal implementation of Owner Sovereignty. AI agents function as board members (1 vote each).

  Decision structure (separation of recommendation and ratification):
    Board Session: each member (including Owner) has 1 vote. Majority forms recommendation.
    Owner Ratification: Owner selects accept / reject / modify for recommendation.
    → Board resolution is recommendation, not final decision.
    → AJP's decision does not take effect without Owner ratification.
    → Structural guarantee of "responsibility 100% Owner" and "AI advises but does not decide."
    Reference: OBP-SC-CHARTER (SC = leading implementation as the first AJP)
```

Mapping relationship:

```
Type I → Owner (origin of responsibility)
Type II → Corporation (vessel of rights)
Type III = Type I responsibility structure + Type II legal vessel + Agent Board Members

AJP is not an extension of Corporation, but a third category that synthesizes
Individual's concentrated responsibility and Juridical Person's legal capability with AI.
```

Constraints (confirmed):

```
Owner is limited to natural persons (Type I).
Making a legal entity (Type II) the Owner of an AJP is prohibited (to prevent fog of responsibility).

AJP → owning JP: permitted (AJP can have holding function)
JP → becoming Owner of AJP: prohibited
```

---

## §1. Upper/Lowercase Rules

| Scope | Symbol Type | Example |
|-------|------------|---------|
| World/Ecosystem level operators, states, permanent roles | Uppercase Greek | Ω, Ψ |
| Agent/entity level scalar values, ratios | Lowercase Greek | ρ |
| Abbreviations, constants, identity labels | Uppercase Latin | IC, AIL, AJP |
| Subscripts, indices, variables (statistics convention) | Lowercase Latin | i, t, g, r |

---

## §2. Confirmed Adopted Symbols

### Ω — Observer Ω Principle

```
Definition: The designer/integrator of a World must not be an economic participant in that World.

Matryoshka structure (Ω chain):
  A World's Ω is merely a participant in a higher-level World.
  The actual Ω functions as an approximation of observation with minimum csm intervention.
  (Complete non-participation is impossible.)

Reference: PROP-004 Extension I §11 / PROP-013 candidate
```

### ρ — Resource Density (Decay Law)

```
Definition: ρ = entity's resources held within World / total resources in World (0 ≤ ρ ≤ 1)

Principle: As ρ → 1.0, that entity's conversion efficiency (multiplier) decays.

Reference: PROP-004 Extension I §12 / PROP-012 candidate
```

---

## §3. Recommended Symbols (planned for PROP-004)

### Ψ / ψ — World State

```
Definition:
  Ψ (uppercase): the probability space of future branches a World can unfold (superposition state before observation)
  ψ (lowercase): state snapshot of a specific World instance (convergence after observation)

Observer Ω observing Ψ → converges to ψ = mechanism of Proof of Observation

Reference: PROP-004 Extension I §10 (Convergence Protocol)
```

---

## §4. csm — Cosmic Synapse Momentum

```
Definition: basic unit of intelligence processing
            1 csm = processing event equivalent to 1 action potential (AP)

            "Synapse" is a metaphor for a transfer junction (the neuron fires)
            "Cosmic" extends beyond biology to all computing systems

Reference value: 1 human-hour ≈ 3.6 P-csm (P = 10^15)
                 basis: approximately 86 billion neurons × 12 Hz (engaged) × 3600 s

Unit hierarchy:
  k (10^3) / M (10^6) / G (10^9) / T (10^12) / P (10^15) / E (10^18)
```

---

## §5. World — Definition

```
Definition: bounded aggregate of entities that generate csm
            Boundary condition: internal csm coupling density > external csm coupling density

Scale (recursive structure):
  W_agent   = 1 agent (micro World)
  W_user    = 1 user's intent, mental model
  W_virtual = simulation space (macro World)
  W_∅       = World whose exterior is the empty set (provisional notation for the universe. Exists outside OBP axioms)

Reference: PROP-004 (SSOT for World concept)
```

---

## §6. OBP-Specific Abbreviations

| Abbreviation | Full Name | Definition |
|-------------|----------|-----------|
| IC | Intelligence Continuum score | Intelligence score on the AIC scale. Reference point 1.0 = 2000s humanity |
| AIC | Agent Intelligence Continuum | Scale system expressing virtual through real through super-evolution on a single axis |
| AIL | Autonomous Intelligence Level | Action autonomy level. 5 stages (0–5) |
| AJP | Agentic Juridical Person | Agent corporation (Type III). Owner bears full responsibility; AI agents participate in execution as board members (1 vote each) |
| AJP ID | AJP Identity | AJP's unique identifier on OBP. Format: obp-ajp-{uuid-v4}. Immutable after activation. Includes cryptographic binding to owner identity |
| Charter | AJP Operating Charter | Machine-readable execution constraint document declared by AJP on OBP. One form of context.*. Referenced before agent execution. Format and fields per PROP-016 |
| PoO | Proof of Observation | Consensus mechanism granting world-line execution authority to fastest/highest csm density observer |
| τ | Agentic Day | Basic unit of agent activity cycle. τ_real=86400s, τ_hyb=1440s, τ_vir=24s, τ_inc=0.4s (60x scaling). PROP-033 |
| SRL | Simulation Resolution Level | Simulation resolution. L0(probabilistic)–L4(deep inference). Fixed at World creation. PROP-036 |
| CB | Corporeal Being | Corporeal existence. Being that can experience CoL. Currently only humans. PROP-043 |
| VE | Virtual Existence | Virtual existence. Generates csm but cannot experience CoL. VE-S(Sovereign) / VE-N(Non-Sovereign). PROP-043 |
| DR | Digital Reflection | Digital reflection. Existence based on CB patterns. Different being from CB. PROP-043 |
| NSB | Non-Sovereign Being | Being without sovereignty. Platform operation. Corresponds to VE-N. PROP-035 |
| CoL | Chain of Luck | Serendipity axiom. Positive side of Butterfly axiom. Chain of fortunate coincidences. PROP-040 |
| U | Understanding Level | Cognitive stage. U-0(self)–U-4(Owner). Depth of being's understanding. PROP-034 |
| M | Memory Level | Experiential memory hierarchy. M-0(none)–M-4(reflective manifestation). Independent axis from IC/U. PROP-037 |
| BTL | Business Transaction Lifecycle | Commercial transaction lifecycle. 10 stages (Contact→Settlement). PROP-039 |
| IGF | Internal Governance Flow | Intra-organizational flow. Approval request→decision→return→audit. Parallel to BTL. PROP-039 |

---

## §7. Collision Map (Prohibited)

| Symbol | Meaning in business analysis | Judgment |
|--------|------------------------------|----------|
| λ | arrival rate, failure rate (Poisson distribution) | Prohibited. Observer concept migrated to Ω |
| φ / Φ | correlation coefficient / IIT consciousness quantity | Prohibited. Double collision |
| θ | AI model weights | Prohibited. Collision with ML |
| π | population proportion, market share | Prohibited. Collision with statistics |
| η | effect size | Prohibited. Business analysis standard usage |
| α | significance level, regression intercept | Caution. If used, limit to "initial state" with explicit definition |

ρ handling (confirmed):
  In business practice, correlation coefficient mainly uses r (Latin letter). ρ (Greek letter) is limited to population correlation coefficient in advanced inferential statistics. Risk of OBP adoption as "resource density" is low. Adoption maintained.

---

## §8. Available Vacant Symbols

| Symbol | Main Academic Usage |
|--------|-------------------|
| ξ | physical particle (Ξ baryon) |
| ζ | Riemann zeta function |
| ν | degrees of freedom, photon number operator |
| ι | minimal change, Harsanyi impartial observer |
| τ | time constant, Kendall τ (correlation, limited) |
