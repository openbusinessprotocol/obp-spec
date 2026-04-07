# OBP-TRUST — Delegation & Authorization Protocol

Status: Adopted (PROP-088, 2026-04-07)

Note: `_v.contract` (VDB record of legal contracts) is a separate concept from this protocol.

---

## 1. Concept Definition

OBP-TRUST is the specification that expresses
"who delegated what to whom within what scope"
as a delegation and authorization chain at the protocol level.

```
OBP-TRUST ≠ _v.contract (VDB record of legal agreement content)
OBP-TRUST =  executable protocol for authorization delegation

Distinction:
  _v.contract  → stores the "legal agreement content" between parties as data
                  (signature date, parties, effective period, etc.)
  OBP-TRUST    → protocol evaluated at runtime to determine "what is permitted"
                  (runtime permission check)
  OBP-SIG      → record of evidence that "the act was actually performed"
                  (post-fact attestation)

  Complete "delegation contract" = contract data + OBP-TRUST grant + OBP-SIG (at signing)
```

"Contract" is a traditional document-centric concept. OBP-TRUST is an executable permission flow.

---

## 2. TRUST Grant Structure

```json
{
  "_obp_type": "OBP-TRUST/1.0",
  "id": "trust:01JRXXX",
  "grantor": "obp:example:user:xxx",     // delegating being (OBP-ID ref)
  "grantee": "obp:example:agent:yyy",    // delegated being (OBP-ID ref)
  "capabilities": [
    "resource.write._v.task",
    "flow.execute.workflow:*",
    "resource.read.*"
  ],
  "constraints": {
    "scope": "workspace:zzz",
    "project": "project:aaa",
    "expires_at": "ISO8601",
    "max_risk_class": "L2",
    "requires_approval_above": "L2"
  },
  "parent_trust_id": "trust:parent",
  "issued_at": "ISO8601",
  "revoked_at": null
}
```

---

## 3. Capability Notation

```
{operation}.{target_scope}.{namespace or ID}

Operations:
  resource.read      read
  resource.write     create / update (excluding delete)
  resource.delete    delete
  flow.execute       workflow execution
  flow.schedule      schedule setting
  being.configure    agent configuration change
  context.override   context override

Examples:
  resource.write._v.task           → create/update tasks
  flow.execute.workflow:wf_001     → execute specific workflow
  flow.execute.workflow:*          → execute all workflows
  resource.write.*                 → write to all resources (broad permission)
```

---

## 4. Delegation Chain

```
Principal (human)
  │ OBP-TRUST grant-A
  ↓
Primary Agent
  │ OBP-TRUST grant-B (can only delegate a subset of grant-A)
  ↓
Sub-Agent / Tool
  │ OBP-TRUST grant-C (subset of grant-B)
  ↓
Execution

Invariant: grantee cannot delegate beyond grantor's capabilities
```

---

## 5. Use Cases vs. Contract Records

| Use Case | What to Use |
|----------|-------------|
| Delegate weekly report creation to agent | OBP-TRUST grant |
| Business commission contract with a counterparty | contract data record + OBP-DOC/contract + OBP-SIG |
| "This agent may send invoices" | OBP-TRUST grant (capabilities: flow.execute) |
| "FY2026 basic commission contract" | contract record + effective_date / expiry_date |

---

## 6. Relationships to Other Protocols (MECE)

```
OBP-ID    → who (being identification)
OBP-TRUST → what is permitted (permission definition)
OBP-SIG   → evidence of what was actually done (attestation)
contract record → data record of agreement content

All four together express a "complete delegation relationship."
```

---

## 7. Invariants

- A grantee cannot delegate capabilities beyond those of the grantor (MUST)
- A grant must specify an explicit scope constraint (SHOULD)
- Revocation must be recorded; revoking a grant does not erase past actions (MUST)
- The delegation chain (parent_trust_id) preserves full traceability to the original principal (MUST)
