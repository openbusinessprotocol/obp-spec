# OBP-WORLD — World: First-Class Concept of Epistemic Context

Status: Adopted (PROP-102, 2026-04-07)
Revisions:
  2026-03-25: PROP-004 adopted. World definition, world_type, correlation_map declaration model, OBP mission extension.
  2026-03-28: PROP-063 adopted. §10 World Tool Manifest added. Agent-environment independence principle and World three-dimensional structure defined.
  2026-04-07: PROP-004 conditions lifted. §11 Convergence Protocol / §12 Observer Ω Principle / §13 Resource Concentration Governance Principle added.

---

## 1. Relationship to Existing OBP

OBP defines the following cosmology:

```
universe = the total set of the OBP Namespace Registry (exists prior to any World Filter)
world    = the "projected reality" that a specific World Filter casts from the universe
```

This specification extends the `world` concept.
The "reality" projected by a World Filter (an OBP-implementing platform) may take the form of
physical world, virtual world, digital twin, or hybrid — and OBP defines this as a first-class concept.

---

## 2. Definition of World

```
World — a unit of epistemic context in which knowledge is generated

All knowledge, activity, and decision-making occurs within some World.
Worlds exist in parallel and hold no hierarchy or privileged origin.
```

Business activity on Earth is simply "one World."
A Mars colony, a virtual simulation, and a digital twin are each independent Worlds.
Treating any one of them as a privileged reference point is contrary to OBP's design principles.

---

## 3. world_type

A World belongs to one of the following four classifications:

| world_type | Description | Examples |
|------------|-------------|---------|
| physical | Real space governed by physical laws | Corporate activity on Earth, a Mars colony |
| simulation | Virtual space governed by designed rules | Game worlds, economic simulations |
| digital_twin | Correlation coefficient to a specific physical World is explicitly declared | Factory digital twin, urban model |
| hybrid | Elements of physical and simulation are mixed | Augmented reality environments, real business on metaverse platforms |

`physical` is not synonymous with "reality." It is a classification indicating that physical laws
(mechanics, economic principles, biology) apply — and is not limited to Earth.

`simulation` worlds may have graduated implementation levels:

| Level | Description |
|-------|-------------|
| V-Entity | Fully virtual space where only virtual entities operate |
| A-Corp | Virtual economy autonomously operated by agent organizations |
| P-Agent | Agents from the physical world use a simulation world for decision-making |

---

## 4. correlation_map — Declaration Model

The transferability of knowledge across Worlds is defined as a "declaration" by the designer.

```
correlation_map: { target_world_id → coefficient(0.0 – 1.0) }
```

- The coefficient is a declared value, not a measured one
- Declared by the World's designer to the protocol
- `1.0` = knowledge is fully transferable (same physics class, same rule system)
- `0.0` = knowledge is non-transferable (completely different rule system)

### Declaration Examples

```
digital_twin_factory.correlation_map[physical_factory] = 0.95
  # Declared to have strong correlation with physical by design specification

mars_colony.correlation_map[earth] = 0.6
  # Declared that Earth-derived economic principles partially apply

game_world_x.correlation_map[earth] = 0.1
  # Declared low transferability due to significantly different physics
```

Correlation coefficients do not indicate superiority. A world with coefficient `0.1` is not
"inferior" — it functions as a caution coefficient when transferring knowledge between
earth and world_x.

---

## 5. Epistemic World Routing

When an agent uses knowledge for decision-making, it calculates transferability by referencing
the correlation_map between the World where the knowledge was generated and the World
where the agent is currently operating. This mechanism is called Epistemic World Routing.

```
transferability = knowledge.world.correlation_map[agent_current_world]
```

When transferability is low, the agent expresses caution in applying the knowledge,
or prioritizes knowledge from a World with higher correlation.

---

## 6. Extension of OBP Mission Definition

Previous:
> A protocol for the operation, development, and harmony of real-world business

v1.0 onward:
> A protocol enabling owners to sovereignly manage knowledge, decisions, and agent delegation
> in any World (physical, simulation, digital_twin, hybrid).
> Cross-World knowledge transfer is explicitly governed by correlation_map.

This extension does not alter OBP's foundational principles (Owner Sovereignty / Avatar Principle).
Responsibility for activity delegated to agents in any World — regardless of which World —
converges back to the owner.

---

## 7. Alignment with Existing OBP Concepts

| Concept | Relationship |
|---------|-------------|
| Owner Sovereignty | Holds across Worlds. Owner's sovereignty does not depend on the active World |
| Avatar Principle | Agents are the owner's avatar. Responsibility for activity in World X converges to the owner |
| UIL knowledge relation 6 types | derived_from / contradicts etc. are also applicable across Worlds |
| context.location | Describes physical location. World is a higher-order epistemic context unit (encompasses context.location) |
| universe | Unchanged. universe = total OBP namespace set. World is an epistemic projection unit within it |

---

## 8. Delegation to Implementations

The following implementation details are the responsibility of World Filter (OBP-implementing platform)
and are not included in this specification:

- Physical design of world storage tables
- Backward compatibility handling for null world references
- Correlation_map declaration authority design (world creator only, or platform updates permitted)
- Migration numbering and timing for world_id references in knowledge stores
- Addition of cross-world variants to OBP-UIL knowledge relation types

Implementation details are delegated to each implementing platform's data specifications.

---

## 10. World Tool Manifest (PROP-063)

A World holds a "capability-scoped runtime" in which agents operate.
This is called the World Tool Manifest.

```
world.tool_manifest[]:
  - tool:    tool name (the name the agent calls)
  - maps_to: the operation that is executed
  - scope:   the range the operation affects
  - mode:    sandboxed | live | mirror | trap

world.execution_context:
  - type:      sandbox | live | mirror | trap
  - isolation: full | partial | none

world.exit_policy:
  - output: destination for results when leaving the World
```

Agent-environment independence principle:

```
Agents do not perceive the World.
Agents simply do their best with "the tools currently available."

The difference between Worlds is expressed only as a diff in tool_manifest.
Agents MUST NOT be implemented with conditional branching based on world_type or
execution_context.type.
```

This principle ensures that an agent executed in a virtual World operates without
additional learning when moved to a live World.
World designers can control agent behavior scope simply by changing the scope and mode
in tool_manifest.

World three-dimensional structure:

```
Epistemic scope (§2–§6 / PROP-004)
  + Value exchange scope (PROP-032)
  + Capability execution scope (this section)
  = the complete structure of a World
```

Implementation details (adapter layer design, procedures for converting external apps into Worlds, etc.)
are the responsibility of the World Filter and are not included in this specification.

---

## 11. Convergence Protocol

A World may define an operation that parallel-simulates multiple future branches
(superposition of World states), evaluates the outcomes, and deploys the best result
to another World (typically a physical World).

```
Procedure:
  1. Expand scenarios in parallel within a simulation world
  2. Evaluate the outcome of each scenario
  3. Deploy the best scenario to the physical world
```

This is an active extension of Epistemic World Routing.
Agents can not only "observe and transfer knowledge" but also
"simulate, select the best outcome, then deploy."

Invariants:
- The deployment decision authority belongs to the Principal (owner). Agents may only propose (MUST).
- Simulation world execution results MUST be disclosed to the Principal before deployment (MUST).

---

## 12. Observer Ω Principle

The designer or integrator of a World must not be an economic participant in that World.

Rationale (from the Butterfly Axiom):
The act of observation itself generates csm (information-energy conversion events).
Complete separation exists only in theory.
Therefore the Observer Ω Principle targets "minimization and transparency of intervention,"
not "complete non-intervention."

Governance application:
- The designer of a World must not profit from transactions within that World
- If an Observer is unavoidably involved in a World, the nature of the intervention must be disclosed
- This is a World-scale version of OBP's impartiality principle (Agent Constitution Article 2)

---

## 13. Resource Concentration Governance Principle (Decay Law)

When a single being moves toward monopolizing resources within a World,
the World should provide a mechanism to decay that being's multiplier by design.

```
When resource concentration exceeds threshold θ:
  being.multiplier *= decay_rate (< 1.0)

  decay_rate is declared by the World designer
  θ is set based on the World's physics_class and design intent
```

Relationship to the ASSET protocol (PROP-084):
OBP-ASSET defines the FORCE threshold for individual beings.
The Decay Law is a World-level resource concentration suppression mechanism.
The two operate complementarily.

Invariants:
- Whether to apply the Decay Law to world_type = physical is at the World Filter's discretion (no MUST NOT)
- Declaration of the Decay Law is recommended for world_type = simulation / digital_twin

---

## 9. Revision History

| Date | Changes |
|------|---------|
| 2026-04-07 | PROP-004 conditions lifted. §11 Convergence Protocol / §12 Observer Ω Principle / §13 Resource Concentration Governance Principle added. world_type notes added (simulation world implementation levels). |
| 2026-03-28 | PROP-063 adopted. §10 World Tool Manifest added. Agent-environment independence principle and World three-dimensional structure defined. |
| 2026-03-25 | PROP-004 adopted. World definition, world_type, correlation_map declaration model, OBP mission extension. |
