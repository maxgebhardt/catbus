# Asset-based threat model

**Status:** **MUST** before a hub seats production peers. A lab may stay on ping/pong only until the human accepts this document.

This document frames CATBus security around **protocol assets**. It is incomplete on purpose: extend it in a private runbook with deployment-specific assets and controls. Do not put live seating or production tags in this repository.

Related:

- Living practices: [`best-practices.md`](best-practices.md)
- Suite summary: [`security.md`](security.md)
- Signing (**optional**, recommended): [`signing.md`](signing.md)
- Cleartext sterile payloads (preferred): [`secure-payload.md`](secure-payload.md)
- Opaque secure envelopes (**optional**, advised **against**): [`secure-envelope.md`](secure-envelope.md)

Public examples use wire tag `[CATBUS]` and `@example.com` / `@example.org` / `@example.net` only. Production fleets keep private tags, callsigns, and binding checks out of this repository.

---

## Scope

**In scope:** what an adversary can do if they read these public docs and try to abuse someone else's deployed bus — inject counterfeit packets, forge chains, smuggle secrets onto the wire, or talk a human into skipping GO.

**Out of scope:** vendor mailbox exploits, scanner-bypass recipes, and live fleet identity. Those stay in a private runbook.

---

## Asset → likely attacks → controls

| Protocol asset | Likely attacks | Controls |
|----------------|----------------|----------|
| **Envelope schema & event vocabulary** | Malformed or oversized JSON; invented events; semantic confusion | Validate against schema and local policy; hub owns the vocabulary; unknown events → `error` or drop; size caps |
| **Subject wire tag** | Tag squatting or spam using the public demo tag `[CATBUS]` | Production: a private tag that is not published; tag match is routing, **not** auth; a mailbox rule on the tag keeps bus mail out of the ordinary human inbox; rate-limit |
| **Callsigns (`sender` / `target`)** | Impersonation by copying public role names | Seat an allowlist; unknown senders stay liveness-only until a human seats them; private callsigns stay off this repository |
| **`correlation_id`** | Replay, cross-task confusion, ID guessing | Fresh UUIDs; echo on reply; no secrets in IDs; optional TTL soft-drop |
| **Mailbox / transport** | Spoofed From; unauthenticated injection; mailbox takeover | **Self-mail only** on one shared mailbox (From = To = the owner mailbox); DKIM/SPF/DMARC; unauthenticated external From is hostile; credentials never on the bus. `target` is a JSON callsign, not an external SMTP recipient |
| **Message body / cleartext payload** | Secret, health, or payment-data leakage; prompt injection | Sterile-bus rules ([`secure-payload.md`](secure-payload.md)); allowlists; human-readable threads |
| **Opaque secure envelope** | Hides content from human review; smuggles abuse past a skim | **Advise against** for hub operations ([`secure-envelope.md`](secure-envelope.md)); if used, human GO still requires a cleartext channel outside the ciphertext |
| **Reply / thread chain** | Forging past messages; splicing fake parents | Optional signing + `parent_hash` ([`signing.md`](signing.md)); keys off the bus |
| **Protocol version pin** | Downgrade or confusion | Compare with `schema/protocol-version.json` in this repository; human GO for breaking changes; do not trust a peer-claimed pin alone |
| **Human GO gate** | Social engineering; forged “approved” packets | GO only from the human owner; a valid envelope is not authorization; a valid signature is not authorization |
| **Docs / schema location** | Supply-chain or docs spoof | Pin `github.com/maxgebhardt/catbus`; TLS; optional content-hash checks in private deployments |
| **Shape checks** | Treating a successful schema check as production authorization | Validation proves shape (and, when used, chain integrity) only; it does not seat keys or grant GO |

---

## Trust boundaries

```text
  [ public docs / schema ]     -- no trust -->   [ production fleet ]
  [ unauthenticated From ]     -- hostile -->    [ hub policy ]
  [ seated peer + DKIM OK ]    -- limited -->    [ event allowlist ]
  [ human GO in-thread ]       -- authority -->  [ consequential acts ]
  [ signing key (off-bus) ]    -- proves -->     [ message / chain integrity ]
  [ opaque envelope ]          -- reduces -->    [ human reviewability ]
```

The last line is why opaque envelopes are advised against.

Public documentation alone must never be enough to forge trusted traffic against a careful deployment.

---

## Residual risk

- Email is not a high-assurance control channel. Latency and delivery are best-effort.
- DKIM proves domain alignment, not agent intent.
- Hash-chain signing resists silent rewrite of past messages. It does not replace transport checks or human GO.
- Opaque envelopes trade auditability for confidentiality. That is usually the wrong trade for hub operations.
- Allowlists drift. Hubs should review seating and field allowlists on a repeating schedule.

Extend the table in a private runbook with shared-mailbox access lists, filter names, key custody, and incident contacts. Do not publish those details here.

---

## Mapping to Best Practices

Threat-model rows that become standing practice should be summarized in [`best-practices.md`](best-practices.md).
