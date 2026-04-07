# OBP-ACT — Action Embedding Protocol

Status: Adopted (PROP-086, 2026-04-07)

---

## 1. Concept Definition

OBP-ACT is the protocol that bridges "read-only" to "immediately executable."
It is the standard definition format for executable actions embedded in
documents, messages, notifications, and dashboards.

OBP-ACT began as `actions[]` within OBP-DOC but became independent because:
- Embedding is needed in OBP-MSG / OBP-NTFY / widgets and other non-OBP-DOC contexts
- The semantics of actions (integration with autonomy gates) are independent of documents

---

## 2. Action Structure

```json
{
  "_obp_type": "OBP-ACT/1.0",
  "id": "uuid-v7",
  "label": "Approve",
  "type": "approve" | "reject" | "sign" | "form" | "payment" | "trigger" | "navigate",
  "target": {
    "flow_id": "workflow-id",
    "form_schema": { ... },
    "url": "...",
    "payment_intent_ref": "..."
  },
  "requires_being": "human" | "agent" | "any",
  "autonomy_gate": "L1" | "L2" | "L3",
  "context": {
    "deadline_at": "ISO8601",
    "min_approvers": 1,
    "ep_ref": "OBP-PROFILE:id"
  },
  "outcome": {
    "on_approve": "OBP-ACT:next-action-id",
    "on_reject": "OBP-ACT:reject-action-id"
  }
}
```

---

## 3. Action Type Details

| type | Meaning | Executor |
|------|---------|---------|
| `approve` | Decision approval | human / L3-agent |
| `reject`  | Rejection / return | human / L3-agent |
| `sign`    | Electronic signature → generates OBP-SIG | human |
| `form`    | Form input request | human |
| `payment` | Payment execution | human / agent |
| `trigger` | Workflow launch | any |
| `navigate`| Screen navigation | human |

---

## 4. Integration with Autonomy Gate

```
The autonomy_gate field maps to risk classification:

  L1 = autonomy_gate: "L1"  → Agent can auto-execute (LOW risk)
  L2 = autonomy_gate: "L2"  → Agent executes, notifies after (MEDIUM)
  L3 = autonomy_gate: "L3"  → Human pre-approval required (HIGH)

Types "approve" / "sign" always require autonomy_gate: "L3" or higher.
```

---

## 5. Embedding Patterns (Loose Coupling)

OBP-ACT recommends reference passing over value passing:

```json
// Embedding in OBP-DOC document (value)
"actions": [ { "_obp_type": "OBP-ACT/1.0", "label": "Approve", ... } ]

// Embedding in OBP-MSG / OBP-NTFY (reference)
"action_refs": [ "obp:act:01JRXXX" ]
```

Reference passing allows multiple documents to share the same action
(e.g., an approval action executable from both notification email and dashboard).

---

## 6. Relationships to Other Protocols

```
OBP-ACT is an "embedded by others" protocol (MECE complement):

  OBP-DOC  → embeds OBP-ACT in actions[]
  OBP-MSG  → embeds OBP-ACT in action_refs[]
  OBP-NTFY → attaches action buttons to notifications

OBP-ACT type: "sign" triggers generation of OBP-SIG.
OBP-ACT type: "payment" integrates with future payment protocols.
```

---

## 7. flow.publish — Publishing Primitive (PROP-007 §A Adopted)

flow.publish is the act of making an OBP-AF Document reachable
to a named receiving context (context.channel).
Executed by a principal with permission; after execution, readers of the target channel can reach the document.

### Type Definition

```typescript
interface OBP_Flow_Publish {
  _type: "flow.publish";
  document_id: string;             // OBP-AF Document ID
  channel_id: string;              // destination channel (context.channel ID)
  publisher_id: string;            // OBP-ID of executing principal
  published_at?: string;           // ISO8601. Immediate if omitted
  rendering_policy?: {
    default_contexts: OBP_AF_Context_Type[];
    excluded_contexts?: OBP_AF_Context_Type[];
  };
  superseded_by?: string;          // subsequent flow.publish ID (tracks overrides)
  revoked_at?: string;             // revocation datetime (ISO8601). Transitions to unpublished state when set
}
```

### Properties

flow.publish is not irreversible.
It can be revoked by setting revoked_at. The fact that revocation occurred remains in OBP history.
For configuration changes, issue a new flow.publish and reference the old instance in superseded_by.

Multi-channel: 1 Document × 1 Channel = 1 instance. Simultaneous delivery to multiple channels generates multiple flow.publish instances.

RBAC: When the executor (publisher_id) and channel owner (channel.owner_id) differ,
permission verification is conceptually defined by OBP and delegated to the application layer for enforcement.

---

## 8. context.prohibition — Mandatory Norm Layer (PROP-017 Adopted)

context.prohibition defines categories of actions that an OBP-compliant system prohibits,
which cannot be overridden even by Owner will.
It is positioned above Charter's action_scope, and Charter cannot relax it.

### Classification Axes

```
AXIS-1: Absolute threshold of irreversibility (completely irreversible actions where temporal_lock cannot function)
AXIS-2: Disappearance of accountability (actions that sever the Identity Chain)
AXIS-3: Re-delegation of Sovereignty (actions that transfer or erase an Owner's identity)
AXIS-4: Attack on OBP protocol itself (subject to known bootstrap limitations)
```

### Position in Execution Flow

```
Flow received
  ↓
prohibition.check (this section)
  ↓ conformant: false → reject + Owner notification + OBP log
  ↓ conformant: true
Charter check
  ↓
OBP-ACT execution
```

---

## 9. flow.coalition — Multi-Agent Boundary Recognition (PROP-018 Adopted)

flow.coalition is not a new flow type.
It is the meta-vocabulary by which OBP "recognizes" structures where multiple flow.* instances execute in chain.

### Type Definition

```typescript
interface OBP_Flow_Coalition {
  coalition_type: "coordinated" | "federated" | "ambient";
  participants: string[];     // participating agent IDs
  initiator_id: string;       // initiating agent ID
  coalition_charter: {        // auto-derived (intersection of all participant Charters)
    action_scope: string[];   // intersection
    csm_ceiling: number;      // min
    tau: number;              // min
    rho_critical: number;     // min
  };
}
```

Owner notification when scope narrows cannot be omitted.
Detection of ambient coalition is a best-effort obligation; the minimum obligation is logging only.
