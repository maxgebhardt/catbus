# Signing protocol (OPTIONAL — recommended)

**Suite status:** **OPTIONAL**. A hub may run without signatures.  
**Recommendation:** For seated machine traffic, use hash-based message signing and reply-chain linking. Signing keeps payloads **cleartext and reviewable** while proving integrity. Opaque secure envelopes ([`secure-envelope.md`](secure-envelope.md)) are a different mechanism and are **advised against** for hub operations.

Related: [`threat-model.md`](threat-model.md) (**MUST**), [`secure-payload.md`](secure-payload.md), [`security.md`](security.md), schema [`../schema/signing.schema.json`](../schema/signing.schema.json).

This document is a generic pattern. It does not prescribe a crypto library or a key-management product.

---

## Goals

1. **Prove** a seated agent produced a given envelope body (integrity, and authenticity relative to a known public key).
2. **Link** replies to parents so an adversary cannot silently splice forged history (**hash-into-hash** / `parent_hash`).
3. Keep **signing private keys off the bus** — never in envelope JSON, mail bodies, or this repository.

Non-goals: replacing DKIM/SPF; confidentiality (use an out-of-band channel for secrets); publishing live key IDs of a real fleet.

---

## When to require signatures

| Mode | Policy |
|------|--------|
| Demo or early lab | Signatures off; still validate envelope shape |
| Seated peers | **Recommend** requiring `signature` on machine traffic |
| Human GO threads | May be plain text per policy; do not confuse GO with a machine signature |

**Signature ≠ authorization.** A verified signature does not authorize spends or third-party outbound mail. Human GO still gates consequential acts.

Signing stays **off** until the human confirms keys are seated. Key exchange and ceremony happen off the bus, never inside a bus envelope.

---

## Private keys off the bus

| On the bus or in this repo (OK) | Off the bus (required) |
|---------------------------------|------------------------|
| `key_id` (opaque handle) | Private signing key material |
| Algorithm name (`ed25519`, `ecdsa-p256`, …) | Key generation, HSM, or agent secret store |
| Public key fingerprint (optional, truncated) | Live key-ceremony details |
| Signature bytes (base64) | Passphrases, seeds, recovery material |

Agents sign locally or via a sealed helper. Only the signature and metadata ride the wire.

---

## Canonical message hash

1. Build the **unsigned envelope object**: all fields except `signature` (and any private binding fields).
2. Canonicalize JSON (recommend UTF-8, sorted object keys, no insignificant whitespace). Document the exact rule in a private runbook and keep it stable.
3. `message_hash = HASH(canonical_bytes)` (recommend SHA-256; SHA-512 or BLAKE2b are possible — declare `hash_alg`).
4. `signature = Sign(private_key, message_hash)` (or sign the canonical bytes — pick one and stay consistent).

Receivers recompute and verify with the sender's seated public key.

---

## Reply chain linking (hash-into-hash)

```text
  H0 = hash(parent_envelope_sans_sig)
  reply includes parent_hash = H0  (inside signed material)
  H1 = hash(reply_envelope_sans_sig)   # covers parent_hash
```

Changing the parent breaks verification of every descendant that linked it.

Optional extensions, kept private if used: Merkle checkpoints, hub-issued chain heads, or periodic `telemetry` of recent tip hashes.

---

## Suggested envelope attachment (illustrative)

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

- `parent_hash` is omitted on chain roots (first `ping` / `intro`).
- `key_id` is an opaque handle, not a secret.
- Example identities only. Do not publish live key IDs. Fictional addresses, if needed in prose, use `@example.com`.

---

## Verification checklist

1. Parse the envelope. Strip `signature` before hashing.
2. Canonicalize → `message_hash`.
3. Resolve `key_id` → seated public key (unknown → reject or liveness-only).
4. Verify `sig`.
5. If `parent_hash` is present, match the stored parent hash (or allow a missing parent when policy permits out-of-order delivery).
6. Apply hub policy and the GO gate.

---

## Failure modes (signing)

| Failure | Suggested hub behavior |
|---------|------------------------|
| Bad signature | Drop or `error`; no payload side effects |
| Unknown `key_id` | Liveness-only until a human seats the key |
| `parent_hash` mismatch | Chain break; alert the human; do not extend trust |
| Missing signature when policy requires it | Reject machine traffic; human plain-text GO may still be allowed by policy |

---

## Relation to transport checks and envelopes

Signing **complements** DKIM/SPF/DMARC and the wire tag. It does **not** replace them. A signed envelope from an unauthenticated external From should still fail transport policy.

Prefer **cleartext sterile payload + signing** over **opaque secure envelopes**. See [`secure-envelope.md`](secure-envelope.md).

Bus packets remain self-mail on the one shared mailbox (From = To = the owner mailbox). Signing does not change that.
