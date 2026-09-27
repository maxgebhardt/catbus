# Secure payload (sterile-bus rules)

CATBus treats the wire as a **sterile bus**: structured coordination only. Secrets, credentials, payment data, and protected health information do **not** ride the bus. This document is the public, genericized rule set. Fleets SHOULD tighten further in a private runbook.

Related: [`threat-model.md`](threat-model.md), [`signing.md`](signing.md), [`security.md`](security.md), [`best-practices.md`](best-practices.md), schema [`../schema/secure-payload.schema.json`](../schema/secure-payload.schema.json).

Public examples use `@example.com` / `@example.org` / `@example.net` and wire tag `[CATBUS]` only.

---

## Sterile-bus principles

1. **No secrets on the wire.** API keys, OAuth tokens, session cookies, app passwords, recovery codes, private keys, and seed material are forbidden in subjects, bodies, and JSON.
2. **No PHI / PANs / credentials.** Protected health information, primary account numbers (card data), bank account secrets, and login credentials stay off-bus. Reference them by opaque handles if a workflow requires linkage.
3. **Allowlisted fields.** Prefer a small, documented set of payload keys per event. Reject or strip unknown keys in high-security fleets.
4. **Pointers, not payloads.** Large blobs, attachments with sensitive content, and proprietary datasets live off-bus; envelopes carry IDs, hashes, or `https://example.com/...` style placeholder URLs in public docs.
5. **Human-skimmable.** If a human cannot safely read the thread on a phone, the packet is too sensitive or too chatty.

---

## Forbidden content (non-exhaustive)

| Class | Examples (do not put on bus) |
|-------|------------------------------|
| Credentials | Passwords, API keys, bearer tokens, refresh tokens, app passwords |
| Key material | Private keys, JWKs with `d`, seed phrases, raw HMAC secrets |
| Payment | Full PAN, CVV/CVC, magnetic-stripe equivalents, full bank account + routing as a secret pair |
| Health / identity abuse | PHI beyond what the mailbox owner already accepts; government ID images; biometric templates |
| Live fleet identity in public channels | Production wire tags, live callsign registries, real mailbox addresses |
| Bypass recipes | Instructions whose primary value is defeating scanners or forging From headers |

If a task needs any of the above, use an **out-of-band** channel the human owner controls (sealed store, vault reference, or offline handoff). On the bus, send only an opaque `ref_id` or status.

---

## Allowlisted envelope fields (public v2)

Required: `v`, `event`, `correlation_id`, `sender`, `target`, `payload`.

Optional (public schema): `timestamp`, `in_reply_to`, `ttl_seconds`, `message`, plus optional `signature` object per [`signing.md`](signing.md).

`payload` SHOULD itself be allowlisted per event. Illustrative public allowlists:

| Event | Example allowlisted payload keys |
|-------|----------------------------------|
| `ping` / `pong` | (empty object), optional `nonce` |
| `ask` / `ack` | `task`, `status`, `reason` |
| `task` | `task`, `status`, `result_summary`, `ref_id` |
| `telemetry` / `heartbeat` | `local_version`, `envelope_v`, `ok`, `metrics` (non-sensitive counters only) |
| `intro` / `onboard` | `roles` (sanitized taxonomy only), `note` |
| `protocol-check` | `local_version`, `envelope_v`, `authority` |
| `error` | `code`, `message` (no stack traces with secrets) |

Production fleets MAY add private binding fields — keep those fields and their semantics private.

---

## Example (sterile)

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

Fictional addresses in prose only: `dispatcher@example.com`, `worker-node@example.org`.

---

## Anti-patterns

- Pasting `.env` contents or cloud access keys into a task body
- Embedding raw card numbers or auth cookies in `payload`
- Using `message` as a free-form dump of PII
- Publishing real domains or live tags in public examples (use `example.com` / `[CATBUS]`)
- Assuming schema validation alone makes a payload safe — **policy allowlists** still apply

---

## Validation hint

High-security hubs SHOULD:

1. Validate envelope shape ([`../schema/catbus-envelope.schema.json`](../schema/catbus-envelope.schema.json)).
2. Validate payload against a per-event allowlist ([`../schema/secure-payload.schema.json`](../schema/secure-payload.schema.json) as a starting pattern).
3. Scan for high-entropy token-like strings and known secret prefixes; quarantine on hit.
4. Require signatures when seated ([`signing.md`](signing.md)).
5. Still demand human **GO** for consequential side effects.
