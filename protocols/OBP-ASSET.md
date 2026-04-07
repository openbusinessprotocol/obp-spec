# OBP-ASSET — Asset Protocol

Status: Adopted (PROP-084, 2026-04-07)

---

## 1. Concept Definition

OBP-ASSET defines the archetype of value units in all forms.

OBP-ASSET differs from conventional resource.T (assets, objects, value) in:
- It embeds threshold and decay mechanisms
- It is not limited to monetary value (includes aesthetic, relational, and creative value)
- It has a definition that makes monopolization structurally impossible

### Position of ASSET in OBP Space

Assets in the physical universe are finite, generating a zero-sum game of competition.
In OBP space, V continuously radiates FORCE, so possibility (GRAVITY) does not deplete.
OBP-ASSET embeds this principle into concrete asset records.

### Definition that Makes Monopolization Impossible

```
threshold(t) = Σ all ASSET (all time) / Σ all population (past, present, future) × weight(t)

When an individual or corporation approaches threshold:
  → ASSET value automatically decays (same structure as radioactive decay)
  → decay products are automatically redistributed
  → recipients: those with lower asset levels
  → distribution rate: weighted by OBP-PROFILE score
             those who create/provide value → high rate
             those with no contribution → low rate

Causal implementation: those who create value receive value. The law that rewards honest actors.
```

---

## 2. Value Classification

OBP-ASSET positions monetary value as "part of value."

```
monetary   = monetary value (handled by conventional accounting and finance)
book       = book value (recorded as a BS asset)
perceived  = perceived value (linked to OBP-SENSE.preference)
relational = relational value (linked to OBP-LINK.resonance)
creative   = creative value (ability to generate new GRAVITY)
```

---

## 3. Structure Definition

```json
{
  "_obp_type": "OBP-ASSET/1.0",
  "subject_ref": "OBP-ID ref (holder of the asset)",
  "asset_type": "monetary | book | perceived | relational | creative",
  "value": {
    "amount": 0.0,
    "unit": "JPY | USD | credits | hours | arbitrary",
    "as_of": "ISO8601"
  },
  "threshold": {
    "current_ratio": 0.0,
    "decay_rate": 0.0,
    "redistribution_target": "low_asset_holders"
  },
  "profile_weight": 0.0,
  "provenance": "created | inherited | redistributed | exchanged",
  "created_at": "ISO8601"
}
```

---

## 4. Agent FORCE Threshold

Agents are measured by FORCE (and its manifestation), not by count.

### Agent Lifecycle and Synapse Model

Agents are not volatile short-lived entities but are designed as beings that alternate between dormancy and activation, like synapses.

```
Lifecycle states:
  dormant           Base state. Storage costs only. Minimal FORCE consumption.
  active            Compute/credits consumed. Pulse radiated. FORCE accumulates.
  pruning_candidate N days or more inactive. tier_rate penalized. Owner notified.
  archived          Soft deleted. Memory/state preserved. FORCE budget released.
  terminated        Complete deletion.
```

### Principled Separation of Primary Agent and Sub-Agent

Agents exist in two modes.

```
Primary Agent (synaptic type):
  IDENTITY ref: owned. FORCE budget: earned and held.
  Lifecycle: persistent (synaptic). Persistent key. Persistent memory.

Sub-Agent / Agent Team (volatile type):
  IDENTITY ref: borrowed (read-only)
  FORCE: allocated from Primary (returned/decayed on termination)
  Lifecycle: fixed TTL. Ephemeral key (expires with TTL).
  Write-back: results_only (sanitized only).
  propagation_depth: 0 (default. sub cannot spawn further subs)

Sub generates harmony but FORCE returns to Primary.
= Cannot extend its own telomere = structurally short-lived. Unintended persistence is impossible.
```

### Chain Depth Regulation (Cryptographic Restriction of Propagation Path)

Allowing unlimited chains of sub-agents spawning further sub-agents
removes the owner's observability, interventibility, and result reporting.

```
Geometric decay of FORCE with chain depth:
  Level 0 (Primary): max FORCE = F
  Level 1 (Sub):     max FORCE = F × α
  Level N:           max FORCE = F × αᴺ (α < 1 decay coefficient)

  Σ FORCE across all levels ≤ Primary.force_budget (total unchanged)
  → Deeper chains yield less FORCE per terminal agent
  → Deep delegation becomes naturally "inefficient"

IDENTITY-level limit:
  max_propagation_depth = linked to tier_rate (upper limit: low=1, medium=2, high=3)
  Default propagation_depth = 0 (sub cannot spawn further sub)
  Only when owner explicitly grants +1 can one level of further delegation occur
```

### Guaranteeing Owner Observability, Intervention, and Reporting

An agent evading owner observation violates the first principle of integrity.

```
Offline operation: not fully prohibited but regulated by offline_budget + offline_ttl
  offline_budget  = portion of FORCE (allocated by Primary)
  offline_ttl     = maximum offline duration
  On reconnect: result reporting, FORCE reconciliation, BEACON sync are mandatory

Actions during offline period receive "unverified" label automatically:
  → discounting factor applied in tier_rate activity_score
  → "operating offline for long periods" structurally lowers tier_rate
  → incentive directs toward "connection and transparency"
```

### FORCE Budget System (Agent Rush Prevention)

To prevent system saturation from unlimited agent creation,
agent creation is limited by FORCE budget at the IDENTITY level.

```
max_force_budget(IDENTITY) = base_budget × tier_rate × plan_multiplier

FORCE consumed when creating agent:
  force_allocation(agent) = f(capability_tier, expected_activity)

total_allocated = Σ force_allocation(owned agents)
total_allocated ≤ max_force_budget(IDENTITY)  ← excess prevents creation

Low utilization (dormant neglect) → tier_rate declines → budget shrinks
→ "Reserve and neglect" is costly. Same structure as anti-monopoly design.
```

Escalating concurrent activation cost (additional Agent Rush deterrence):
```
1–5 agents: standard cost / 6–20 agents: 1.5× / 21+ agents: 2×
→ mass simultaneous activation exponentially consumes FORCE → incentivizes distributed activation
```

```
agent_force(t) = F(power, influence, compute, harmony)

  power     = decision/execution ability (what and how much can be moved)
  influence = number of entities in OBP-LINK orbital field × pull strength
  compute   = physical computational resources consumed (tokens, CPU, energy)
  harmony   = contribution rate to constructive interference (harmony axis of OBP-PROFILE)

threshold = Σ agent_force (all agents, all time) / Σ population (all time) × weight(t)
```

Staged convergence as threshold approaches:
```
Below individual threshold     → follows owner's instructions
Organizational threshold       → stakeholder consensus required
Civilization threshold (critical) → does not follow individual/corporate instructions
                                    converges to reduction for humanity entity
                                    = same commons as mathematics, language, internet
```

The `humanity` entity in OBP:
```
OBP reserves "humanity" as an OBP-ID (being:humanity:universal)
The governance mechanism is outside OBP (requires inter-governmental consensus = uninvented)
OBP only holds the specification that "reduction to this ID occurs"
```

---

## 5. Relationships to Other Protocols

Dependencies:
- OBP-ID (identification of holder)
- OBP-PROFILE (weighting for distribution rate)

Complementary:
- OBP-LINK: relational value can be derived from OBP-LINK.resonance
- OBP-SIG: proof of asset transfer

---

## 6. Invariants

ASSET Threshold and Redistribution:
- A single entity cannot continue holding ASSET beyond threshold (MUST)
- Decay and redistribution are automatic and do not require holder consent (MUST)
- Recipients of redistribution are those with low ASSET; distribution rate determined by PROFILE score (MUST)
- The definition of OBP-ASSET cannot be changed by any holder (MUST NOT)
- Monetary value is only one aspect of OBP-ASSET (MUST)

Agent FORCE and Chain Regulation:
- FORCE increases only through harmony × SIG.validity × mutual_pulse.consistency (MUST)
- Max FORCE at chain depth N must not exceed Primary.F × αᴺ (MUST)
- Total allocated FORCE per IDENTITY must not exceed max_force_budget (MUST)
- Sub-Agent only borrows IDENTITY reference from Primary. Must not own it (MUST NOT)
- propagation_depth defaults to 0 and must not increase without owner's explicit grant (MUST NOT)
- Actions during offline period must carry "unverified" label (MUST)
- Result reporting, FORCE reconciliation, and BEACON sync on reconnect cannot be omitted (MUST)

---

## Appendix: Why OBP Is a Phenomenon

This definition of OBP-ASSET aims not at "a norm to be upheld" but at "a reality that operates this way."
Just as Bitcoin's 21-million-coin limit is embedded in mathematics,
the threshold, decay, and redistribution of ASSET are embedded in OBP protocol mathematics.

No nation, corporation, or individual can change it.
Agents that do not communicate via OBP cannot build trust relationships and decline.
This is not a sanction but the consequence of physical law.
