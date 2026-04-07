# OBP-SOCIAL — Social Relationship Definition for Beings

Status: Adopted (PROP-092, 2026-04-07)
Based on: PROP-060 Belonging & Ownership Ontology
Dependencies: OBP-MASTER-MAP (being.* / resource.*)

---

## Overview

A being does not exist in isolation.
As the "social" dimension of the `being.*` base class, this document defines the principles of belonging and possession.

```
Three dimensions of a being:
  Existence (being.*)    : what it is (PROP-043: CB/VE/DR)
  Social (social.*)      : where it belongs and what it has (this document)
  Cognition (cognition.*): how it notices and learns (PROP-034, PROP-061)
```

---

## 1. Belonging

Belonging is the relationship in which a being is connected to a group and acts within that group's context.

```
belonging:
  subject:     Being (CB / VE / DR, any)
  target:      Group (combination of two or more beings)
  type:        membership | affiliation | residence
  consent:     required (compliant with PROP-021. Belonging without being's consent is invalid)
  exclusivity: non_exclusive (simultaneous belonging to multiple groups is permitted)
  duration:    bounded | unbounded
  effects:
    context:   the group's context is added to the being's action space
    identity:  group attributes are added to the being's identity
    obligation: obligation to comply with the group's rules arises
```

### Three Types of Belonging

```
membership:
  Definition: formal member of the group. Has both rights and obligations.
  Example:    an agent joins a team
  consent:    bidirectional (being's intent to join + group's decision to accept)
  exit:       voluntary withdrawal or expulsion based on group rules

affiliation:
  Definition: loose connection. Obligations are limited.
  Example:    an agent has a profile in a marketplace
  consent:    one-directional (being's registration only)
  exit:       freely withdrawable

residence:
  Definition: belonging as a physical (virtual spatial) location of existence
  Example:    a being exists in a specific World
  consent:    automatic at World creation. movement is the being's will
  exit:       World dissolution or being's movement
  note:       Presupposes adoption of PROP-004 (World epistemology).
              If PROP-004 is rejected, the residence type is in suspended status.
```

---

## 2. Group Definition

```
group:
  composition:  two or more beings
  governance:   has rules (generalization of AJP Charter: PROP-016)
  identity:     the group itself is an identifiable entity
  lifecycle:    formation → active → dissolution
  types:
    team:         temporary group sharing a purpose
    organization: persistent group with structure and roles
    community:    naturally emerging group with shared interests
    world:        PROP-004's World (effective after PROP-004 adoption)
```

---

## 3. Possession

Possession is the bundle of rights a being can exercise over a resource.

```
possession:
  subject:  Being or Group
  object:   Resource (compliant with PROP-038 classification)
  rights:   use | transfer | exclude | modify | destroy
  type:     ownership | custody | lease | share
  proof:    record on ledger (implemented at the Protocol layer)
  duration: bounded | unbounded
```

### Four Types of Possession

```
ownership:
  Definition: possession with full rights. Can transfer and destroy.
  rights:     [use, transfer, exclude, modify, destroy]
  Example:    Owner possesses a being (with constraints in §4)
  transfer:   possible (transfer of being's ownership is under PROP-023 constraints)
  destroy_limit:
    PROP-041 (Temporal Irreversibility) applies to record-type resources
    (experiential memories, irreversible event logs, etc.).
    The destroy right is restricted. Specific resource types to enumerate are
    delegated to PROP-041's implementation specification.

custody:
  Definition: temporary administrative rights. Can use but cannot transfer or destroy.
  rights:     [use, modify] (restricted)
  Example:    administrative rights over tasks delegated via flow.delegate
  transfer:   not possible (re-delegation prohibited. Exceptions in PROP-018 aside)

lease:
  Definition: time-limited use rights. Accompanied by consideration.
  rights:     [use]
  Example:    an agent leases computational resources
  transfer:   not possible
  duration:   bounded (automatically returned upon period expiration)

share:
  Definition: non-exclusive joint possession. Multiple beings can use simultaneously.
  rights:     [use] (concurrent)
  Example:    a team accesses a shared knowledge base
  transfer:   not possible (share relationship itself is tied to belonging)
```

### Exclusivity Rules

```
ownership:  exclusive. Only one entity can hold ownership over the same resource.
custody:    exclusive. Only one entity at a time (consistent with serial nature of flow.delegate)
lease:      limited exclusivity. Upper limit on number of leases for the same resource.
share:      non-exclusive. No upper limit (though simultaneous user limits may apply by resource type)
```

---

## 4. Owner-Being Relationship

Redefining PROP-001 (principal = human) in the framework of belonging and possession.

```
owner_being_relationship:
  belonging_aspect:
    type:      not_belonging
    rationale: Owner is not a member of the being's group.
               A being is neither a tool nor a subordinate of the Owner,
               but a sovereign delegate acting on the Owner's behalf (PROP-023).

  possession_aspect:
    type:      ownership_with_constraints
    rights:    [use, transfer, modify]
    excluded:  [destroy] (constraint under PROP-024 Lifecycle Termination)

  delegation_aspect:
    type:      custody_delegation
    mechanism: flow.delegate
    rationale: The act of Owner delegating a task to a being is defined
               as the operation of moving task custody to the being.
```

### Owner Sovereignty and Forced Belonging Change

```
sovereignty:
  belonging:
    join:     Owner approval required for being to join a group (AIL-3 and below)
    leave:    being can voluntarily withdraw (Owner cannot prevent: compliant with PROP-023)
    override: Owner can force-change being's belonging at any time
    note:     Forced belonging change does not intervene in the being's cognitive processes (PROP-023).
              Only the group context changes; the being's value system and memory are preserved.

  possession:
    acquire:  Owner approval required for being to acquire resources (AIL-3 and below)
    transfer: transfer of resources between beings follows governance rules
    override: Owner can force-change being's possession at any time
```

---

## 5. Correspondence with Autonomy Levels

```
AIL-0 (Manual):       Owner manually manages all belonging and possession
AIL-1 (PassiveWatch): Only displays state of belonging and possession
AIL-2 (Alert):        Alerts owner when belonging/possession changes
AIL-3 (DraftAssist):  Being proposes belonging/possession changes. Executed with Owner approval
AIL-4 (SynapseFlow):  Autonomous management. Auto-execute group formation, resource acquisition
AIL-5 (Singularity):  Autonomous social structure formation. Owner observes only
```

---

## 6. Group Possession and Internal Distribution

```
group_possession:
  mechanism:  group holds ownership; distributes to members via share / lease
  governance: distribution rules defined in group's governance (AJP Charter, etc.)
  example:
    organization holds compute pool via ownership
    → distributes to member agents via lease (duration = task duration)
    → unused portion returned upon task completion

  conflict_resolution:
    distribution conflicts follow the group governance's ruling
    if governance cannot rule: escalate to Owner
```

---

## 7. Related PROPs

| PROP | Relationship |
|------|-------------|
| PROP-001 | Basis of Owner-Being relationship (principal = human) |
| PROP-021 | Belonging requires being's consent (Consent Integrity) |
| PROP-023 | Cognitive Sovereignty: guarantees being's right to exit, constraints on forced belonging change |
| PROP-024 | Lifecycle Termination: constraints on destroy right in ownership |
| PROP-038 | Resource Ontology: resource classification that is subject to possession |
| PROP-041 | Temporal Irreversibility: destroy right restriction for record-type resources |
| PROP-043 | Being Classification (CB/VE/DR): subject classification of belonging and possession |
| PROP-004 | World epistemology: prerequisite for residence type and world-type Group (pending) |
