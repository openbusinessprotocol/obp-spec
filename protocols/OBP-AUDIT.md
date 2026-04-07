# OBP-AUDIT — Immutable Agent Action Ledger

Status: Adopted (PROP-090, 2026-04-07)
Dependencies: OBP-SIG, OBP-ID, OBP-TRUST

---

## 1. Concept Definition

OBP-AUDIT is the specification that defines the immutable continuous record (Immutable Ledger)
of "what an agent did" at the protocol level.

```
OBP-AUDIT ≠ log (mere recording)
OBP-AUDIT =  hash-chained ledger (tampering computationally detectable)

Position (MECE):
  OBP-ID    → who (being identification)
  OBP-TRUST → what is permitted (permission definition, pre-execution)
  OBP-SIG   → evidence of what was actually done (individual act signature / attestation)
  OBP-AUDIT → immutable continuous record of all actions (hash-chain ledger, post-fact guarantee)
```

OBP-AUDIT guarantees the input data quality for the Track record layer of Trust Score.
If Track record is tamperable, Trust Score has no basis.

---

## 2. Design Intent

### Why It Is Needed

Agents act without human supervision. Unless third parties can verify
"that the action was really performed by that agent" and "that it was not later tampered with,"
agents cannot be trusted with delegation.

OBP-AUDIT guarantees the following three things:

| Guarantee | Description |
|-----------|-------------|
| Completeness | Actions are recorded without omission |
| Integrity | Records have not been tampered with afterward |
| Non-repudiation | "That agent performed it" can be verified by a third party |

### Relationship to Blockchain

OBP-AUDIT does not require blockchain as an implementation.
It specifies that OBP-compliant implementations can achieve
"tamper detection" and "non-repudiation" through more lightweight methods.

```
Full blockchain: fully distributed, public verification (high cost)
     ↑
Merkle Root anchoring: weekly anchoring to public chain (middle ground)
     ↑
PostgreSQL Hash Chain: hash chain within a single DB (pragmatic solution)
```

---

## 3. OBP-AUDIT Entry Structure

### 3.1 Basic Format

```json
{
  "_obp_type": "OBP-AUDIT/1.0",
  "id": "audit:01JRXXX",

  "prev_entry_id": "audit:01JRWWW",
  "prev_hash": "sha256:abc123...",

  "actor": {
    "_obp_ref": "obp:example:agent:yyy"
  },
  "action": "flow.execute.workflow:wf_001",
  "resource": {
    "_obp_ref": "obp:example:resource:task:zzz"
  },

  "trust_ref":  "trust:01JRXXX",
  "sig_ref":    "sig:01JRYYY",

  "outcome": "success" | "failure" | "partial",
  "payload_digest": "sha256:...",

  "recorded_at": "ISO8601",
  "entry_hash":  "sha256:sha256(id + prev_hash + actor + action + recorded_at + payload_digest)"
}
```

### 3.2 Field Definitions

| Field | Required | Description |
|-------|----------|-------------|
| `prev_entry_id` | Yes | Previous entry ID (null for first entry) |
| `prev_hash` | Yes | `entry_hash` of previous entry (core of hash chain) |
| `actor` | Yes | OBP-ID reference (agent or user) |
| `action` | Yes | OBP action performed (OBP-TOOL-KEY-TAXONOMY notation) |
| `resource` | No | Target resource (OBP-ID reference) |
| `trust_ref` | No | Reference to OBP-TRUST grant used |
| `sig_ref` | No | Reference to corresponding OBP-SIG (individual signature) |
| `outcome` | Yes | Execution result |
| `payload_digest` | Yes | Hash of execution payload (content protection) |
| `entry_hash` | Yes | Hash of this entry (includes `prev_hash`) |

---

## 4. Hash Chain Protocol

### 4.1 How Hash Chaining Works

```
Entry N-1
  entry_hash: sha256("...content_n-1...")  ─────────┐
                                                     │
Entry N                                              │
  prev_hash: sha256("...content_n-1...") ←──────────┘  ← mismatch = tampering confirmed
  entry_hash: sha256(prev_hash + "...content_n...")  ─────────┐
                                                               │
Entry N+1                                                      │
  prev_hash: sha256(prev_hash + "...content_n...") ←──────────┘
```

### 4.2 Tamper Detection

If entry N is tampered with:
1. N's `entry_hash` changes
2. Mismatch with N+1's `prev_hash` arises
3. All entries from N+1 onward become `INVALID`

Scanning the chain alone detects which entry was tampered with and when.

### 4.3 Verification Algorithm

```
fn verify_audit_chain(from_entry_id, to_entry_id):
  entries = SELECT ordered by recorded_at WHERE id BETWEEN from AND to
  for i, entry in entries:
    if i == 0: continue
    expected_prev_hash = entries[i-1].entry_hash
    if entry.prev_hash != expected_prev_hash:
      RAISE AuditIntegrityError(entry.id, entries[i-1].id)
    if entry.entry_hash != sha256(entry.prev_hash + entry.canonical_content):
      RAISE AuditHashError(entry.id)
  RETURN OK
```

---

## 5. Classification: Which Actions to Record in OBP-AUDIT

OBP-compliant implementations MUST record the following actions in OBP-AUDIT:

| Action Category | Example | Recording Requirement |
|----------------|---------|----------------------|
| `flow.execute.*` | WF execution | Required |
| `resource.write.*` | VDB record write | Required (risk_class L2 or higher) |
| `resource.delete.*` | Record deletion | Required |
| `being.configure.*` | Agent configuration change | Required |
| `flow.delegate.*` | Delegation execution | Required |
| `context.override.*` | Context override | Required |
| `resource.read.*` | Read access | Optional (implementation-dependent) |

An OBP-compliant implementation where `Required` actions are not recorded in OBP-AUDIT is a specification violation.

---

## 6. Complementary Relationship with OBP-SIG

```
OBP-SIG:   "signature proof" of individual act (point)
OBP-AUDIT: "continuous record" of all acts (line)

Acts proven by OBP-SIG have their sig_ref stored in OBP-AUDIT.
OBP-AUDIT also records acts without sig_ref (system acts, automated execution, etc.).

flow.execute + OBP-SIG(witness) = individual "execution proof"
           + OBP-AUDIT          = incorporation into time-series continuous record
           ↓
Ready to be used as Trust Score Track record input data
```

---

## 7. Merkle Tree External Anchoring (Optional)

An optional implementation for enhanced trust.

```
Periodic batch:
  entries = OBP-AUDIT entries from the period
  root_hash = build_merkle_tree(entries).root
  publish(root_hash) to Arweave/Ethereum/IPFS

→ "At this time, these entries existed" becomes externally verifiable
→ Tampering by DB administrator also becomes detectable (bridge to full distribution)
```

---

## 8. Relationship with OBP-ID: Agent Signing Key

When the `actor` in OBP-AUDIT is `OBP-ID agent` type,
it is recommended to sign the `entry_hash` with that agent's ed25519 public key:

```json
"actor_signature": {
  "algorithm": "ed25519",
  "signature": "base64:...",
  "public_key_ref": "obp:example:agent:yyy#key-1"
}
```

This establishes non-repudiation: "this entry was generated by that agent."

---

## 9. Physical Placement and Distribution Requirements

### 9.1 Physical Guarantee of Append-Only

Storage complying with OBP-AUDIT must physically prohibit
UPDATE / DELETE from the application layer (including service roles).

```
RLS policies alone are insufficient: service roles bypass RLS.
Required: raise an exception via BEFORE UPDATE OR DELETE trigger.
```

```sql
-- OBP-AUDIT §9.1 physical append-only enforcement
CREATE TRIGGER trg_audit_logs_append_only
  BEFORE UPDATE OR DELETE ON {audit_table}
  FOR EACH ROW EXECUTE FUNCTION fn_audit_immutable_guard();
```

### 9.2 Distribution Requirements

OBP-AUDIT implementations must satisfy the following requirements for future distributed recording:

Required (from initial deployment):

| Requirement | Reason | Implementation |
|-------------|--------|----------------|
| UUIDv7 (time-embedded ID) | Naturally sortable across distributed nodes | `id DEFAULT uuid_generate_v7()` |
| OBP-ID format actor identification | Globally unique being reference | `actor_obp_id text` |

Recommended (later phase):

| Requirement | Reason |
|-------------|--------|
| Elevate hash chain scope to platform unit | Cannot combine distributed chains at workspace unit |
| `chain_id` chain identifier | Support coexistence and verification of multiple chains |
