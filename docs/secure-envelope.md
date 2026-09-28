# Secure envelope protocol (OPTIONAL — advised against)

**Naming.** These three terms are different:

| Term | Meaning |
|------|---------|
| **Envelope** (CATBus v2) | The normal cleartext JSON object (`v`, `event`, `correlation_id`, …). It stays human-readable. |
| **Secure payload / sterile payload** | Rules for what may appear in cleartext `payload`. See [`secure-payload.md`](secure-payload.md). |
| **Secure envelope (opaque)** | An optional wrapper that encrypts or otherwise hides payload bytes from ordinary inbox review. **This document.** |

---

## Suite status

| | |
|--|--|
| **Status** | **OPTIONAL** |
| **Recommendation** | **Advise against** for normal hub operations |
| **Why** | Opaque envelopes remove human review of the thread. Review of plain JSON, including human **GO**, is part of how this protocol is operated. Prefer **cleartext sterile payloads** plus **optional signing** ([`secure-payload.md`](secure-payload.md), [`signing.md`](signing.md)). |

Threat model (**MUST**): [`threat-model.md`](threat-model.md).

---

## When a secure envelope might still be considered

Rare cases only, with the tradeoff stated:

- A short secret pointer must move and cannot wait for an out-of-band handoff, and the mailbox is not an acceptable cleartext surface.
- An intermediate mail server must not see one field. An out-of-band store is still the better path.

Even then: keep the **outer** CATBus envelope cleartext (`event`, correlation, sender, and target visible). Put opacity only in a nested field if you must. Document recovery for the human owner. Never use opacity to bypass GO.

---

## If you implement anyway (pattern only)

Illustrative. This is not a recommendation to enable the feature.

```json
{
  "v": "2",
  "event": "task",
  "correlation_id": "550e8400-e29b-41d4-a716-446655440030",
  "sender": "dispatcher",
  "target": "worker-node",
  "payload": {
    "secure_envelope": {
      "alg": "age-recipient-or-equivalent",
      "kid": "opaque-recipient-handle",
      "ciphertext": "BASE64_OPAQUE_BYTES"
    },
    "status": "sealed"
  }
}
```

Rules if a human explicitly enables it:

1. The outer envelope stays schema-valid and readable (`event`, parties, and correlation visible).
2. Do not expect the hub to route on inner meaning without decrypting.
3. Human GO for consequential acts stays on a **cleartext** channel. GO cannot live only inside ciphertext.
4. Keys stay off the bus. Do not publish live key identifiers in public docs.
5. Default policy: **reject** `secure_envelope` unless the human owner explicitly enables it.

---

## Preference hierarchy

1. **Threat model** — required
2. **Cleartext sterile payload** — use this
3. **Message signing + chain hash** — optional, recommended
4. **Opaque secure envelope** — optional, **advised against**

For hub operations, reviewability is a security control. Opacity is not the default and is not a stronger mode of this protocol.
