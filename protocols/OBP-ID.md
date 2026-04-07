# OBP-ID — Identity & Principal Protocol

Status: Adopted (PROP-082, 2026-04-07)
Also known as: Principal Protocol

---

## 1. Concept Definition

OBP-ID is the protocol that standardizes "proof of existence" in OBP.
It is the concrete implementation specification of the four base class `being.*`,
defining the common type that all OBP-* protocols use when referencing "who."

```
Concrete forms of being.* (A):
  human   → OBP-ID/user
  agent   → OBP-ID/agent
  org     → OBP-ID/org
  system  → OBP-ID/system (internal services, etc.)
```

---

## 2. Principal Identifier System

```
obp:{realm}:{type}:{local_id}

Examples:
  obp:example:user:01JRXXXX     ← platform user
  obp:example:agent:01JRYYY     ← platform agent
  obp:example:org:ws_ZZZZZ      ← organization (workspace unit)
  obp:external:user:google:xxx  ← user via external IdP
```

`realm` = domain of the World Filter implementation
`type`  = category of being
`local_id` = unique ID within the realm (UUID v7 recommended)

---

## 3. OBP-ID Record Structure

```json
{
  "_obp_type": "OBP-ID/1.0",
  "id": "obp:example:agent:01JRYYY",
  "being_type": "user" | "agent" | "org" | "system",
  "display_name": "...",
  "realm": "example",
  "external_refs": [
    { "provider": "google", "sub": "1234567890" }
  ],
  "public_key": "ed25519:...",
  "created_at": "ISO8601",
  "capabilities_ref": "OBP-TRUST:grant-id"
}
```

`public_key` is used for signature verification in OBP-SIG. Required for agents.

---

## 4. Relationships to Other Protocols

```
OBP-ID  ← referenced by others (foundation)
  ↑
  ├── OBP-TRUST (who delegates what → referenced as being_id)
  ├── OBP-SIG   (who signed → referenced as signer.being_id)
  └── OBP-DOC   (creator_user_id / creator_agent_id → OBP-ID ref)
```

OBP-ID is the "referenced" protocol. All other OBP-* depend on it.

---

## 5. External Compatibility

| External Standard | Compatibility Approach |
|-------------------|----------------------|
| W3C DID | `id` format can be extended for DID compatibility (`did:obp:example:agent:xxx`) |
| OAuth2 / OIDC | `external_refs` holds `sub`. Federation expressed as realm=external |
