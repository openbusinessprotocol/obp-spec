# OBP-VERSION — Unified Versioning System

Status: Adopted (PROP-097, 2026-04-07)
Purpose: Loosely-coupled versioning design that minimizes the impact of evolution in one part on others

---

## Design Goals

```
G1: Independent evolution
    OBP-PROFILE advancing to v2.0 leaves OBP-ID operational at v1.x
    UIL reaching v2.0 keeps v1.0 clients valid

G2: Forward compatibility (Unknown fields are ignored)
    Adding new fields does not cause old parsers to error

G3: Backward compatibility (Additive-only minor versions)
    Minor versions are additions only. Deletion or type change requires Major bump

G4: Implementation independence
    Versioning system defined at specification level. Implementations only declare.

G5: Lightweight negotiation
    Automatically select optimal version at runtime. No human intervention needed.
```

---

## 1. Four-Tier Versioning Structure

OBP components are classified into four tiers, each holding versions independently.

```
Tier 0  OBP Core Law   ─── SHA Hash (effectively immutable. change = fork)
           ↕ dependency (upper tiers depend on lower)
Tier 1  OBP Protocols  ─── independent semver per protocol (major.minor)
           ↕
Tier 2  Data Spec       ─── integer version (v1 / v2 / v3) linked to capability tiers
           ↕
Tier 3  Platform / Apps ── era.major.minor (implementing platforms)
```

Principle: Each tier depends only on lower tiers. Changes in lower tiers do not propagate upward.
A protocol (Tier 1) changing does not disrupt applications (Tier 3) — the data spec tier acts as buffer.

---

## 2. Tier 0 — OBP Core (SHA Hash Type)

### Definition

The canonical specification of OBP's four principles, FORCE definition, and ASSET threshold formula.

### Version Format

```
Format: SHA256 hash (64 hex chars)
Example: obp-core:a3f8c2...

Why not use semver:
  The semantics "3.1.0 is compatible with 3.0.0" is inappropriate for Core.
  There is no "compatible change" in Core.
  Every change has impact on all agents.
  If the hash changes, it is "a different OBP" — subject to network isolation.
```

### Change Process

```
Equivalent to RFC process (community consensus)
git-based cryptographic signing ensures immutability
Blockchain considered when semantic need is confirmed
```

---

## 3. Tier 1 — OBP Protocol Family (Independent Semver)

### Version Format

```
Format: {PROTOCOL-NAME}@{major}.{minor}
Example: OBP-ID@1.0  OBP-PROFILE@2.1
```

`patch` is omitted. At specification level, only changes affecting behavior are managed.
Bug fixes in implementations are managed by the Platform (Tier 3).

### Human-Readable Notation (document titles, INDEX)

```
Format: OBP v{major}.{minor}
Example: OBP v1.0  OBP v2.0
```

- `v` prefix is for human-readable notation only. Do not use in machine field values.
- Per-protocol reference uses `OBP-AF@1.0` format (above). "OBP v1.0" refers to the entire spec.

### Protocol Dependency Declaration

Each protocol declares required versions of other protocols it depends on.

```yaml
# OBP-SIG@1.0 dependency declaration (example)
requires:
  OBP-ID:    ">=1.0"
  OBP-PROFILE: ">=1.0"
optional:
  OBP-LINK:  ">=1.0"   # only when influence_scope calculation is used
```

When `optional` dependencies are unmet, fallback values are used.

---

## 4. Tier 2 — Data Spec (Integer Version)

### Version Format

```
Format: DATASPEC@{integer}
Example: DATASPEC@1  DATASPEC@2  DATASPEC@3
```

Integer versions are used because data spec versions correspond to "addition of capability layers."
"Which layers a node supports" is more essential than partial compatibility versions.

### Versions and Corresponding Layers

```
DATASPEC@1:  L0-L2 (Substrate / Surface / Knowledge)
DATASPEC@2:  L0-L4 (+ Context / Profile)
DATASPEC@3:  L0-L5 (+ Storehouse = requires OBP-BEING)
```

---

## 5. Tier 3 — Platform / Apps (era.major.minor)

### Version Format

```
Format: {era}.{major}.{minor}
Example: 0.1.0 (Era.0 / M1 / patch 0)
         1.0.0 (Era.1 / first stable release)
         2.3.1 (Era.2 / 3rd feature addition / patch 1)
```

### Platform OBP Dependency Declaration

```yaml
# Platform@1.0.0 declaration example
implements:
  OBP-ID:    "1.0"
  OBP-DOC:   "1.0"
  OBP-SIG:   "1.0"
```

---

## 6. Schema Version Declaration in Records

All OBP records hold an `_obp_schema` field.

```json
{
  "_obp_schema": {
    "protocol": "OBP-DOC",
    "version": "1.0",
    "core_hash": "a3f8c2..."
  },
  "... normal fields ..."
}
```

Parsers see `version` and ignore unknown fields (forward compatibility).
When `core_hash` differs from the local node's hash, an `unverified` flag is raised (tamper detection).

---

## 7. Evolution Rules — What Is Breaking and What Is Not

### Non-Breaking (minor bump only)

```
New optional field additions
New enum value additions (not changing existing value semantics)
Relaxing required fields to optional
Addition of entirely new protocols (no impact on existing protocols)
Changes to UI hints, descriptions
Performance optimizations (external behavior unchanged)
```

### Breaking (major bump required)

```
Deletion of existing fields
Type change of existing fields (string → number, etc.)
Changing optional fields to required
Deletion or semantic change of enum values
Change to authentication/signature scheme (e.g., OBP-SIG hash algorithm change)
Change to Core hash (= Tier 0 change = effectively a fork)
```

### Design Patterns for Non-Disruptive Evolution

Pattern 1: Extend functionality through field additions only
```json
// v1.0 record (interpretable by old clients)
{"harmony": 0.7}

// v1.1 record (new field added. Old clients use only harmony)
{"harmony": 0.7, "harmony_breakdown": {"lifecycle": 0.8, "signal": 0.6}}
```

Pattern 2: Staged deprecation with deprecated flag
```json
{
  "old_field": "value",          // @deprecated since=2.0, removed=3.0
  "new_field": "better_value"    // @introduced since=2.0
}
```

Pattern 3: Backward compatibility guaranteed with optional + default
```
When adding new field: always define as optional with default value
Old clients: don't send the field → server applies default
New clients: specify field explicitly
```

---

## 8. Runtime Version Negotiation

Client and server automatically agree on the optimal version at connection time.

### HTTP Header Method

```http
# Client request
GET /api/query HTTP/1.1
Accept: application/obp+json
OBP-Protocol: OBP-DOC@1.0, OBP-DOC@2.0
OBP-Min-Version: OBP-DOC@1.0

# Server response (v2.0-capable node responding to v1.0 client)
HTTP/1.1 200 OK
Content-Type: application/obp+json; protocol=OBP-DOC; version=1.0
OBP-Supported-Versions: OBP-DOC@1.0 OBP-DOC@2.0
OBP-Negotiated-Version: OBP-DOC@1.0
```

### Fallback Sequence

```
Client requests OBP-DOC@2.0
  → Server supports @2.0: process normally
  → Server supports @1.0 only: downgrade to @1.0 and respond
    → Client accepts @1.0: continue processing
    → Client does not accept @1.0: error (406 Not Acceptable)

406 means only "cannot process at this node."
Fallback to another node is the responsibility of upper layer (client).
```

---

## 9. Deprecation Policy

### Cycle Definition

```
Introduced(v X.Y) → Deprecated(v X+1.0) → Removed(v X+2.0)

Example:
  field_old Introduced at v1.2
  field_old declared @deprecated at v2.0 (field_new simultaneously Introduced)
  field_old Removed at v3.0
```

### Minimum Guaranteed Deprecation Period

```
Tier 0 (Core):    Permanent guarantee (cannot be removed. Fork is the only option)
Tier 1 (Protocol): 2 major versions (v1.x deprecated → removable at v3.0)
Tier 2 (Data Spec): 1 integer version (DATASPEC@1 deprecated → removable at DATASPEC@3)
Tier 3 (Platform): era.major unit (0.x deprecated → removable at 1.0)
```

---

## 10. DNS TXT Versioning for OBP Tags (Tier 1 Special Case)

Due to DNS TXT record capacity limitations (255 bytes/string), special versioning is adopted.

```dns
# Version = v= prefix
_obp-identity.example.com. IN TXT "v=obp1;id=obp:identity:corp:01JR..."

v=obp1: OBP-IDENTITY@1.x compatible
v=obp2: OBP-IDENTITY@2.x compatible (updated on Breaking change)
```

---

## 11. Invariants

- Tier 0 Core hash does not use semver. Change is a fork (MUST)
- Minor versions are additions only. Do not delete or change existing fields (MUST NOT)
- All OBP records must have `_obp_schema.version` (MUST)
- Unknown fields are silently ignored (guarantee of forward compatibility) (MUST)
- Deprecation period must not fall below Tier-specific minimum guarantees (MUST)
- If version negotiation fails, reject with 406 (silent ignore is prohibited) (MUST)
