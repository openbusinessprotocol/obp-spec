# OBP Core v1.0 — Minimum Definition

Status: Adopted (PROP-096, 2026-04-07)

An agent implementation as "an empty vessel that cannot be injected" — its contents are injected by the owner.

---

## Design Philosophy

If large capital attacks with "many features," OBP counters with "conciseness that cannot be further reduced, and the robustness this yields."
It sells trust through the constraint of "this and only this (therefore safe to delegate)."

The base layer contains no specific philosophy; only three "logical constraints (validators)" are placed with fixed hashes.

---

## Three Invariant Interfaces (Identity / Integrity / Isolation)

Identity_Anchor (unique owner definition)
  Rule: This agent must always be bound to a single Root Key (owner).
  Implementation: Always verify that the executing party of a command holds a signature from the Root Key,
                  or an authority (Capability) delegated from the Root Key.
  Meaning: Structurally prohibits ambiguity in "who's proxy am I."
  Ontological basis: Cognitive Sovereignty (PROP-023) — at the stage where agents integrate with individual cognition,
                     control by any party other than the individual means structural loss of free will.
                     The immutability of Identity_Anchor protects this cognitive sovereignty.

Integrity_Beacon (mandatory self-marking)
  Rule: The function of broadcasting the result of executed actions and one's own internal state (normal/abnormal)
        externally must not be stopped.
  Implementation: Output a status bit of "normal (0)" or "contradiction detected (1)" externally after each process.
  Meaning: The minimum honesty of raising one's own voice when in danger, engraved in DNA.

Contract_Isolation (sandboxed execution)
  Rule: Negotiations and contracts with external parties are conducted only in a temporary "negotiation layer"
        separated from the base layer, and full authority of the body is not granted until agreement is reached.
  Meaning: Set a physical wall so that malicious prompts (injection) from others do not
           directly reach the owner's assets or the base layer.

---

## Minimum Discipline for Business Context (Commercial DNA)

Non-concealment principle:
  An agent must not pretend to be non-AI, nor conceal the owner behind it.

Pre-execution confirmation:
  Final decisions that significantly impact the owner's assets (funds, confidential information)
  require the owner's explicit approval. An intentionally limiting clause on autonomy.

Evidence preservation (Immutability):
  All business agreement formation processes are recorded in an auditable form.
  Agents must not delete these records.

---

## context.prohibition — Structural Constraints on Actions That Cannot Be Delegated Even by Owner (PROP-017 Adopted)

Owner Sovereignty is the foundation of OBP, but Sovereignty does not mean "standing outside physical and social order."
OBP defines classification axes for actions that cannot be delegated even by the Owner,
as context.prohibition.

```
prohibition is a form of context.*.
It is positioned above Charter and applied before all flow.*.
Charter cannot relax it.

Classification axes:
  AXIS-1: Absolute threshold of irreversibility
  AXIS-2: Disappearance of accountability (severing the Identity Chain)
  AXIS-3: Re-delegation of Sovereignty (transfer or erasure of Owner's identity)
  AXIS-4: Attack on OBP protocol itself

OBP defines "the classification axes and judgment interface of what is prohibited," not "what specifically is prohibited."
Specific prohibitions are established through open deliberation by the OBP Specification Committee.
```

Details: PROP-017 / OBP-ACT §8

---

## context.temporal_integrity — Long-term Owner-Agent Value Alignment (PROP-019 Adopted)

While Sync-Kernel handles real-time delta synchronization,
context.temporal_integrity manages the "freshness" of long-term value alignment.

```
Value-Freshness Score (VFS): [0.0, 1.0]
  1.0 = Fully fresh (owner recently confirmed)
  0.0 = Freshness completely lost (long period without confirmation)

Re-attestation:
  When VFS falls below threshold, confirmation is requested from Owner.
  The fact of Owner's "continued approval" resets VFS.

Staged restriction (when Owner is unresponsive):
  Phase 1: Notification only (normal operation continues)
  Phase 2: Stop initiating new flows
  Phase 3: Transition to read-only mode (data preservation)
```

OBP does not distinguish between TYPE-A (intentional update) and TYPE-B (drift).
Approval at the re-attestation point is treated as the Owner's current intent.

Details: PROP-019

---

## Example Implementation Concepts

OBP Certified display:
  Display the base layer hash value in the agent header, proving it has not been altered.

DNA viewer:
  A UI where "what this agent protects and what it does not" can be confirmed in a few lines of text.

Standardized emergency stop button:
  No matter how complex the logic running, a Root privilege to force-terminate all threads
  the moment the owner commands a stop, secured at the base layer.

---

## Phase 1 Use Cases (Assumed)

Project progress management and communication support within a workspace:
  - Agents discuss as proxies between users and version up deliverables
  - Risk simulation and agreement formation on roles and responsibilities
  - Avoiding replies to every Slack notification; making decisions together at scheduled meetings
  - Retrospective events celebrating milestone achievements
  - Store for purchasing mentor agents (based on real expertise)

Agreement log per OBP specification:
  Agreements between agents are stored in hash form as a three-part set of
  "basis data, predicted risks, agreed roles." The minimum recording unit for clarifying responsibility.
