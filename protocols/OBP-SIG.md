# OBP-SIG — Signature & Attestation Protocol

Status: Adopted (PROP-089, 2026-04-07)

---

## 1. Concept Definition

OBP-SIG is the protocol that attaches to any OBP document
via an envelope pattern to record "who authenticated or signed what."

```
OBP-SIG = Attestation Envelope

  OBP-DOC (semantic content)   ← WHAT (the semantic content of the document)
       +
  OBP-SIG (attestation)        ← WHO CERTIFIED THIS
       =
  Legally binding electronic document / approved workflow / audit trail
```

OBP-SIG does not change the content. It attaches
the immutable trace of "this existed, was agreed to, was executed."

---

## 2. OBP-SIG Structure

```json
{
  "_obp_type": "OBP-SIG/1.0",
  "id": "sig:01JRXXX",
  "document": {
    "_obp_ref": "obp:af:01JRAAA"
  },
  "document_digest": "sha256:...",
  "canonical_form": "JCS",
  "signatures": [
    {
      "signer_id": "obp:example:user:xxx",
      "signed_at": "ISO8601",
      "intent": "approve" | "sign" | "witness" | "countersign" | "acknowledge",
      "algorithm": "ed25519",
      "signature": "base64:...",
      "ep_ref": "obp:ep:yyy"
    }
  ],
  "policy": {
    "required_count": 1,
    "threshold_type": "all" | "any" | "n-of-m",
    "n": null
  },
  "status": "pending" | "complete" | "expired" | "rejected",
  "expires_at": "ISO8601",
  "created_at": "ISO8601"
}
```

---

## 3. Complementary Relationship with OBP-DOC

```
The use of OBP-SIG varies by OBP-DOC profile:

  profile: "default"    → OBP-SIG: optional (for approval flow confirmation)
  profile: "contract"   → OBP-SIG: required (signature needed for legal effect)
  profile: "report"     → OBP-SIG: optional (when distributing as "approved report")
  profile: "slide"      → OBP-SIG: optional (finalized presentation)

OBP-SIG in workflows:
  change_request.approved    → OBP-SIG intent: "approve"
  contract.signed           → OBP-SIG intent: "sign" (all parties)
  report.acknowledged       → OBP-SIG intent: "acknowledge"
  workflow.autonomy_event   → OBP-SIG intent: "witness" (evidence of autonomous agent execution)
```

---

## 4. Signature Intent

| intent | Meaning | Legal Effect |
|--------|---------|-------------|
| `sign` | Formal signature. Confirms agreement/contract | Maximum |
| `approve` | Superior's approval. Permits execution | High |
| `countersign` | Counter-signature after counterparty's signature | High |
| `witness` | Third-party record of "observed an act" | Medium |
| `acknowledge` | Record of receipt/confirmation (not agreement) | Low |

---

## 5. Agents as Signers

Agents can also be signers in OBP-SIG (OBP-ID agent type):

```
When an agent autonomously executes an OBP-DOC/contract:
  → grantor (human) delegates "resource.write._v.contract" via OBP-TRUST
  → agent generates OBP-SIG intent: "sign"
  → recorded in OBP-AUDIT
  → reported to human via notification

autonomy_gate L3 = human's `sign` is required
autonomy_gate L2 or below = agent can `sign` (requires TRUST delegation)
```

---

## 6. Signature Verification

```
Verification flow:

  1. Recompute OBP-SIG.document_digest (normalize per canonical_form)
  2. Compare with stored digest → tampering detected
  3. Retrieve signer's public_key from OBP-ID
  4. Verify signature with public_key (ed25519)
  5. Confirm signer has capability for that intent via OBP-TRUST
```

---

## 7. Relationships to Other Protocols (MECE)

```
OBP-SIG is an "envelope that wraps others" (needed whenever someone must certify):

  OBP-DOC/contract + OBP-SIG = electronic contract
  OBP-DOC/report   + OBP-SIG = approved report
  flow.execute    + OBP-SIG = audit trail (to OBP-AUDIT)
  OBP-ACT:approve + OBP-SIG = immutable record of approval act

OBP-ID    → who signed (resolution of signer_id)
OBP-TRUST → whether that person/agent has signing authority
OBP-PROFILE → emotional context at signing time (ep_ref)
```
