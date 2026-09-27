# Signing schema (generic hash-based)

CATBus public signing is **hash-based message integrity** with **private keys kept off the bus**. This document describes a generic pattern fleets MAY adopt. It does not prescribe a single crypto library or key-management vendor.

Related: [`threat-model.md`](threat-model.md), [`secure-payload.md`](secure-payload.md), [`security.md`](security.md), optional schema [`../schema/signing.schema.json`](../schema/signing.schema.json).

---

## Goals

1. **Prove** a seated agent produced a given envelope body (integrity + authenticity relative to a known public key).
2. **Link** replies to parents so an adversary cannot silently splice forged history into a trusted chain (**hash-into-hash**).
3. Keep **signing private keys off-bus** — never in envelope JSON, mail bodies, or public docs.

Non-goals: replacing DKIM/SPF, providing confidentiality (use out-of-band channels for secrets), or publishing live key IDs / fingerprints of a real fleet here.

---

## Private keys off-bus

| On bus (OK) | Off bus (required) |
|-------------|--------------------|
| `key_id` (opaque handle) | Private signing key material |
| Algorithm name (e.g. `ed25519`, `ecdsa-p256`) | Key generation / HSM / agent secret store |
| Public key fingerprint (optional, truncated) | Full key ceremony details for a live fleet |
| Signature bytes (base64) over the canonical message hash | Passphrases, seed phrases, recovery material |

Agents sign locally (or via a sealed helper). Only the signature and metadata ride the wire.

---

## Canonical message hash

1. Build the **unsigned envelope object**: all envelope fields except `signature` (and any private binding fields your fleet adds).
2. Canonicalize JSON (recommend: UTF-8, sorted object keys, no insignificant whitespace — document the exact rule in your private runbook and keep it stable).
3. Compute `message_hash = HASH(canonical_bytes)` (recommend SHA-256; fleets MAY use SHA-512 or BLAKE2b — declare `hash_alg`).
4. `signature = Sign(private_key, message_hash)` (or Sign over the canonical bytes — pick one and stay consistent).

Receivers recompute `message_hash` from the received envelope (minus `signature`), verify with the sender's seated public key.

---

## Reply chain linking (hash-into-hash)

To resist forging or rewriting past messages in a thread:

1. Parent message has `message_hash` \(H₀\).
2. Child (reply) includes `parent_hash: H₀` (or equivalent) **inside** the signed material.
3. Child's own `message_hash` \(H₁\) therefore **binds** the parent's hash: changing the parent breaks verification of every descendant that linked it.

```text
  H0 = hash(parent_envelope_sans_sig)
  reply includes parent_hash = H0  (signed)
  H1 = hash(reply_envelope_sans_sig)   # covers parent_hash
```

Optional extensions (private): Merkle checkpoints, hub-issued chain heads, or periodic `telemetry` of recent tip hashes.

---

## Suggested envelope attachment (illustrative)

Public demos may attach a `signature` object; production field names can differ if privately documented.

```json
{
  "v": "2",
  "event": "ack",
  "correlation_id": "550e8400-e29b-41d4-a716-446655440010",
  "sender": "worker-node",
  "target": "orchestrator",
  "in_reply_to": "ask",
  "payload": { "status": "accepted" },
  "signature": {
    "alg": "ed25519",
    "hash_alg": "sha256",
    "key_id": "worker-node-2026q3",
    "parent_hash": "0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef",
    "sig": "BASE64_SIGNATURE_BYTES_HERE"
  }
}
```

Notes:

- `parent_hash` is omitted on chain roots (e.g. first `ping` / `intro`).
- `key_id` is an opaque seat handle, not a secret.
- Example domains only: `orchestrator@example.com` style identities stay off public examples unless fictional.

---

## Verification checklist

1. Parse envelope; strip `signature` before hashing.
2. Canonicalize → `message_hash`.
3. Resolve `key_id` → seated public key (unknown key_id → reject or liveness-only).
4. Verify `sig` over `message_hash` (or canonical bytes).
5. If `parent_hash` present: confirm it matches the stored hash of the claimed parent (or policy-allow missing parent for out-of-order delivery).
6. Apply normal hub policy (event allowlist, GO gate). **Signature ≠ authorization for consequential acts.**

---

## Failure modes

| Failure | Suggested hub behavior |
|---------|------------------------|
| Bad signature | Drop or `error`; do not execute payload side effects |
| Unknown `key_id` | Liveness-only until human seats key |
| `parent_hash` mismatch | Treat as chain break; alert human; do not extend trust |
| Missing signature when fleet policy requires it | Reject machine traffic; allow human plain-text GO threads per policy |

---

## Relation to transport auth

Signing complements, does not replace, DKIM/SPF/DMARC and private wire tags. A signed envelope from an unauthenticated From should still fail transport policy in careful deployments.
