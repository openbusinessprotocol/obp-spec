# OBP-IDENTITY — Real-World Identity Anchor Protocol

Status: Adopted (PROP-099, 2026-04-07)
Related: OBP-ID (technical reference ID)

---

## 1. Concept Definition

OBP-IDENTITY is the anchor protocol that guarantees "real-world uniqueness" in OBP space.

Role distinction from OBP-ID:

```
OBP-ID       = technical reference identifier. All entities have one. Lightweight.
OBP-IDENTITY = real-world uniqueness anchor. Only humans and legal entities hold one. Heavyweight.

Relationship:
  OBP-IDENTITY ⊃ OBP-ID
  IDENTITY embeds OBP-ID as primary_id.
  OBP-ID can exist without IDENTITY, but IDENTITY cannot exist without OBP-ID.
```

### Why OBP-IDENTITY Is Needed

OBP-ASSET threshold calculation is applied per entity.
With OBP-ID (UUID) alone, a single real-world party could generate many agents to
disperse and evade thresholds — a Sybil attack.

OBP-IDENTITY solves the following:

```
1. Sybil attack prevention:
   1 real-world party = 1 IDENTITY
   FORCE of all agents owned/controlled by that party aggregates to IDENTITY
   → Threshold is calculated by FORCE total, not agent count

2. Legitimacy of threshold calculation:
   Threshold applies per "real-world person or organization" unit
   Without IDENTITY, civilization-threshold convergence cannot function

3. Completeness of TRUST chain:
   Makes it possible to identify the final "real-world party" who delegated to agents in OBP space
```

---

## 2. Targets of IDENTITY

Only "parties existing in the real world" can hold OBP-IDENTITY.

```
Targets:
  human   → natural person (individual)
  corp    → legal entity (corporation, organization, NPO, government agency, etc.)

Non-targets (OBP-ID only):
  agent   → agents (owned by IDENTITY holders)
  system  → internal services
  platform → the platform itself

humanity → reserved in OBP-ID space as being:humanity:universal (§6)
```

---

## 3. Identifier System (3 Layers)

OBP-IDENTITY has three layers of identifiers, used according to Trust Level.

```
Layer A  Basic identifier (all IDENTITYs have this)
  Format: obp:identity:{type}:{uuid-v7}
  Example: obp:identity:human:01JRZZZZZZ
           obp:identity:corp:01JRAAAAAA

Layer B  Domain notation (those who have DNS-issued domain)
  Format: {reverse-domain}:{type}:{local-id}
  Example: com.example:corp:01JRAAAAAA

Layer C  DID compatible (future extension)
  Format: did:web:{domain}:{type}:{local-id}
  Example: did:web:example.com:corp:01JRAAAAAA
           did:obp:identity:corp:01JRAAAAAA
```

---

## 4. DNS-Native Issuance Model

OBP-IDENTITY issuance and publication is based on DNS.

Participation requirement: internet connectivity and DNS publication only.

This is the fundamental difference from Proof of Personhood (PoP) systems.
No Orb device, biometric authentication, gas fees, or guarantors are needed.
Organizations with a domain can obtain Trust Level 1 today.
By requiring no central authority, censorship resistance and autonomous verification are achieved.

### 4.1 TXT Record Structure (3 Layers by Purpose)

DNS TXT records are split by purpose.
Each record is kept short, allowing only the relevant layer to be fetched.

```dns
; Layer 0: Discovery (always public, required, short)
_obp-identity.example.com. IN TXT "v=obp1;id=obp:identity:corp:01JRAAAAAA;t=2"

; Layer 1: Verification (registry number binding, required)
_obp-identity-v.example.com. IN TXT "v=obp1;r=jp:corporate:0100-01-012345;h=none"

; Layer 2: Authentication (for high-assurance authentication, optional)
_obp-identity-a.example.com. IN TXT "v=obp1;auth=webauthn,totp;bio=platform"
```

Field specification:

```
v=obp1          protocol version (for future migration)
id=             OBP-IDENTITY ID (discovery layer only)
t=              Trust Level (0–3)
r=              registry reference ({country}:{type}:{number})
h=              hash (corporation: none / individual: {algo}:{hash})
auth=           available authentication methods (webauthn / totp / biometric)
bio=platform    device biometric (WebAuthn platform authenticator)
```

### 4.2 Hash Policy

Corporate registration number: no hash required (h=none)
```
Corporate numbers are "public information" by design (searchable by anyone)
Adding a hash does not create confidentiality
Triple verification (§4.4) ensures binding, so cryptographic protection is unnecessary
```

Individual number: quantum-resistant hash required
```
Adopted algorithm (priority order):
  b3:{hex}    BLAKE3 (recommended. quantum-resistant, fast, 256-bit)
  sha3:{hex}  SHA-3-256 (alternative)
  pq:{hex}    future post-quantum standard (reserved)

Principle: prefix algorithm identifier before hash value
Reason: cryptography always becomes obsolete. Identifier allows seamless migration.
Migration: run old and new algorithm records in parallel, switch gradually
```

### 4.3 Countermeasures Against Spoofing Attacks

```
Attack examples:
  0100-01-012345 vs 0100-01-O12345 (zero vs letter O)
  Unicode normalization attack (full-width numbers, ligatures, combining characters)
  Brute-force ignoring check digits

Countermeasures:
  1. Strict character set: record values allow only [0-9a-z:.\-] (lowercase normalization required)
  2. Unicode prohibited: ASCII only. Non-ASCII characters immediately rejected
  3. Check digit verification: corporate numbers have mod-11 check digit
     → Format verification alone eliminates most typos and spoofing
  4. Version prefix v=obp1: handles format changes
```

### 4.4 Well-Known Endpoint (Detail Layer)

```
https://{domain}/.well-known/obp-identity.json
```

```json
{
  "_obp_type": "OBP-IDENTITY/1.0",
  "id": "obp:identity:corp:01JRAAAAAA",
  "layer_b": "com.example:corp:01JRAAAAAA",
  "trust_level": 2,
  "registry_refs": [
    {
      "registry": "jp:corporate",
      "id": "0100-01-012345",
      "hash": "none"
    }
  ],
  "primary_obp_id": "obp:example:org:ws_XXXXXXX",
  "beneficial_owner_ref": "obp:identity:human:01JRZZZZZZ",
  "owned_agents": [],
  "public_key": "ed25519:...",
  "created_at": "ISO8601"
}
```

### 4.5 Triple Verification Flow (Countermeasure Against TXT Spoofing)

DNS TXT records are self-declarations.
Trust is ensured by triple verification with the fact that "the registration authority lists the official site URL."

```
Step 1: DNS TXT lookup (verification layer)
        _obp-identity-v.example.com → r=jp:corporate:0100-01-012345

Step 2: Registry API query
        Corporate number registry → corporate number 0100-01-012345's registered URL = example.com

Step 3: well-known query
        https://example.com/.well-known/obp-identity.json → declaration of same number

All three match → verified (no additional confirmation to central authority needed)
```

When Step 2 "registry lists official URL" cannot be established:
- Trust Level 2 treated as "self-declaration + domain ownership proof" (with tier_rate discount)
- Trust Level 3 (regulated TLDs like co.jp) has Step 2 guaranteed by prior review

---

## 5. Trust Level

OBP-IDENTITY trust strength is defined in 4 levels.
Used for FORCE calculation weighting.

```
Level 0  UUID only (self-declaration. no external verification)
         → FORCE calculation: 0.5 × raw_force (50% discount)

Level 1  DNS domain ownership only
         → FORCE calculation: 0.75 × raw_force (25% discount)
         → Layer B identifier becomes available

Level 2  DNS domain + external registration number (corporate registration number, etc.)
         → FORCE calculation: 1.0 × raw_force (full calculation)
         → registry_refs holds verified number

Level 3  Regulated TLD (co.jp / gov / ac.uk, etc.) + external registration number
         → FORCE calculation: 1.0 × raw_force + harmony_bonus
         → Highest trust. Maximum influence in FORCE calculations
```

### External Registration Number Format

Uniqueness comes from "real-world public registration authorities" (co.jp model).

```
jp:corporate:{corporate number}     # Japanese corporations (National Tax Agency)
jp:individual:{BLAKE3(personal ID)} # Japanese individuals (hash required)
us:ein:{EIN}                        # US corporations (IRS EIN)
uk:companies-house:{number}         # UK corporations (Companies House)
eu:vat:{VAT number}                 # EU corporations (VIES)
...
```

DNS uniqueness limitations (multiple domain holdings) are complemented by external registration number uniqueness.
Corporate and personal numbers guarantee "1 party = 1 number" uniqueness in the real world.

---

## 6. FORCE Aggregation and Threshold Calculation

OBP-ASSET threshold calculation is executed with IDENTITY as the unit.

```
effective_threshold(IDENTITY) = base_threshold(t) × tier_rate(IDENTITY, t)

identity_force(IDENTITY) = Σ agent_force(a) for all a ∈ owned_agents(IDENTITY)
                         × trust_level_weight(IDENTITY.trust_level)

owned_agents(IDENTITY) = all agents owned/controlled by IDENTITY
```

Because threshold is calculated by total FORCE, not agent count:
- Generating many UUID agents does not disperse FORCE (Sybil attack ineffective)
- Even if one agent has enormous FORCE, it aggregates to IDENTITY

### Tier Rate (Activity Reality Multiplier)

To counter attacks registering multiple legal entities to increase headroom,
a `tier_rate` (0.0–1.0) multiplier is applied to the threshold.

```
tier_rate(IDENTITY, t) = weighted composite of:
  activity_score   agent activity volume, continuity, diversity
  trust_score      mutual recognition with other verified IDENTITYs / OBP-LINK.resonance
  longevity_score  existence period and consistency of IDENTITY activity
  harmony_score    OBP-PROFILE harmony axis (contribution rate to constructive interference)

Newly registered shell company: tier_rate ≈ 0.05
Organization with substantial activity: tier_rate ≈ 0.7–1.0
Party with accumulated mutual trust: tier_rate = 1.0
```

To game this, one must "accumulate real business activity" — which is exactly what OBP encourages.
Attack cost converts into legitimate cost.

### Beneficial Owner

As additional defense against the multiple-entity control problem,
legal entity IDENTITY includes `beneficial_owner_ref` (reference to natural person IDENTITY).

```
Future aggregation model:
  human IDENTITY as ultimate anchor
  FORCE of corporations that human controls aggregated at the human level
  → threshold double-checked at the natural person level

This is migrating the real-world "beneficial owner" concept
(AML regulation KYC) into OBP.
```

---

## 7. Relationship with Agents

```
OBP-IDENTITY (real-world party)
  └── owns → OBP-ID/agent × n (agent group)
               FORCE aggregates to IDENTITY

Agent ownership change:
  An agent is owned by at most one IDENTITY.
  Ownership transfer is recorded via OBP-SIG-signed delegation.
```

---

## 8. Relationships to Other Protocols

Dependencies:
- OBP-ID (IDENTITY embeds OBP-ID as primary_id)

Dependent on IDENTITY:
- OBP-ASSET: threshold calculation unit is identity_force
- OBP-TRUST: identification of the final delegating responsible party
- OBP-SIG: confirmation of real-world attribution of the signing party

---

## 9. Invariants

- 1 real-world party (human / corp) can hold only 1 OBP-IDENTITY (MUST)
- FORCE from owned_agents cannot be excluded from IDENTITY (MUST NOT)
- Real identification numbers of individuals must always be stored hashed (MUST)
- Corporate numbers do not require hashing (public information) (MAY store plaintext)
- trust_level cannot be self-upgraded without external verification (MUST NOT)
- The humanity entity (being:humanity:universal) does not hold IDENTITY (MUST NOT)

---

## Appendix: Why Not Centrally Managed

OBP-IDENTITY leverages the fact that uniqueness is "already guaranteed by public registration authorities."

```
Corporate number uniqueness → guaranteed by tax authorities and each country's government
Individual number uniqueness → guaranteed by personal numbers, SSN, etc.
Domain attribution          → guaranteed by DNS / ICANN

OBP only "standardizes the procedures and formats to reference these."
No new central authority is created. Existing trust infrastructure is reused.
```

This means:
- OBP itself does not become a single point of failure
- Naturally aligns with each country's legal framework
- High censorship resistance (even if a government stops OBP, registration numbers remain)
