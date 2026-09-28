# Secure payload — sterile cleartext (preferred)

**Naming:** This document is about **cleartext, human-reviewable payloads** on a sterile bus. It is **not** an opaque secure envelope. For the optional opaque construct (advised against), see [`secure-envelope.md`](secure-envelope.md).

**Suite status:** Recommended baseline for all hubs (pair with the threat model, which is **MUST**). Signing is optional and recommended on top. Opaque envelopes are optional and advised against.

Related: [`threat-model.md`](threat-model.md), [`signing.md`](signing.md), [`secure-envelope.md`](secure-envelope.md), [`security.md`](security.md), schema [`../schema/secure-payload.schema.json`](../schema/secure-payload.schema.json).

Public examples use `@example.com` / `@example.org` / `@example.net` and wire tag `[CATBUS]` only.

Bus packets are self-mail on one shared mailbox. A mailbox rule on the wire tag keeps those packets out of the ordinary human inbox so a person can still skim them on purpose.

---

## Sterile-bus principles

1. **No secrets on the wire.** API keys, OAuth tokens, cookies, app passwords, recovery codes, private keys, and seeds are forbidden in subjects, bodies, and JSON.
2. **No health data, payment numbers, or credentials.** Reference them by opaque handles off the bus if a workflow needs a link.
3. **Allowlisted fields.** Use a small documented set of payload keys per event. Reject or strip unknown keys in higher-security deployments.
4. **Pointers, not blobs.** Large or sensitive blobs stay off the bus. Envelopes carry IDs, hashes, or `https://example.com/...` placeholder URLs in public docs.
5. **Human-readable.** If a human cannot safely read the thread, the packet is too sensitive or too chatty. That is why cleartext is the baseline and opaque envelopes are advised against.

---

## Forbidden content (non-exhaustive)

| Class | Examples (do not put on the bus) |
|-------|----------------------------------|
| Credentials | Passwords, API keys, bearer or refresh tokens, app passwords |
| Key material | Private keys, JWKs with `d`, seed phrases, raw HMAC secrets |
| Payment | Full card number, CVV, magnetic-stripe equivalents, secret bank pairs |
| Health / identity documents | Health data beyond what the mailbox owner already accepts; ID images; biometrics |
| Live fleet identity in public docs | Production wire tags, live callsign registries, real mailbox addresses |
| Bypass recipes | Scanner defeat or From-forgery recipes |

Use an out-of-band channel the human owner controls for the above. On the bus, send only an opaque `ref_id` or a status.

---

## Allowlisted envelope fields (public v2)

Required: `v`, `event`, `correlation_id`, `sender`, `target`, `payload`.

Optional: `timestamp`, `in_reply_to`, `ttl_seconds`, `message`, and optional `signature` ([`signing.md`](signing.md)).

| Event | Example allowlisted payload keys |
|-------|----------------------------------|
| `ping` / `pong` | (empty), optional `nonce` |
| `ask` / `ack` | `task`, `status`, `reason` |
| `task` | `task`, `status`, `result_summary`, `ref_id` |
| `telemetry` / `heartbeat` | `local_version`, `envelope_v`, `ok`, `metrics` (non-sensitive counters only) |
| `intro` / `onboard` | `roles` (sanitized taxonomy only), `note` |
| `protocol-check` | `local_version`, `envelope_v`, `authority` |
| `error` | `code`, `message` (no secret stack traces) |

Deployments may add private binding fields. Keep those fields and their meaning private.

---

## Example (sterile cleartext)

Subject: `[CATBUS] REQ: ask`

```json
{
  "v": "2",
  "event": "ask",
  "correlation_id": "550e8400-e29b-41d4-a716-446655440020",
  "sender": "dispatcher",
  "target": "worker-node",
  "payload": {
    "task": "summarize",
    "ref_id": "doc-example-001"
  }
}
```

Fictional addresses in prose only: `dispatcher@example.com`, `worker-node@example.org`. The SMTP envelope for bus traffic is still self-mail on the one shared mailbox. `target` is not an external recipient.

---

## Anti-patterns

- Pasting `.env` contents or cloud access keys into a task body
- Embedding card numbers or auth cookies in `payload`
- Using `message` as a free-form dump of personal data
- Publishing real domains or live tags in public examples
- Assuming schema validation alone makes a payload safe
- Wrapping ordinary hub traffic in opaque envelopes ([`secure-envelope.md`](secure-envelope.md))

---

## Validation hint

1. Validate envelope shape ([`../schema/catbus-envelope.schema.json`](../schema/catbus-envelope.schema.json)).
2. Validate payload against a per-event allowlist ([`../schema/secure-payload.schema.json`](../schema/secure-payload.schema.json) is a starting pattern).
3. Scan for high-entropy token-like strings. Quarantine on a hit.
4. Optionally require signatures when keys are seated ([`signing.md`](signing.md)).
5. Still require human **GO** for consequential side effects. GO stays in cleartext.
