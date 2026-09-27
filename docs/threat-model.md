# Asset-based threat model (public, non-exhaustive)

This document frames CATBus security around **protocol assets**, not product marketing. It is intentionally incomplete: fleets SHOULD extend it in a private runbook with deployment-specific assets and controls.

Related:

- Living practices: [`best-practices.md`](best-practices.md)
- Public security notes: [`security.md`](security.md)
- Signing: [`signing.md`](signing.md)
- Sterile payloads: [`secure-payload.md`](secure-payload.md)

Public demos use wire tag `[CATBUS]` and `@example.com` / `@example.org` / `@example.net` only. Production fleets keep private tags, callsigns, and binding checks out of this repo.

---

## Scope

**In scope:** what an adversary can do if they read these public docs and try to abuse *someone else's* carefully deployed bus (inject counterfeit packets, forge chains, smuggle secrets onto the wire).

**Out of scope (here):** vendor-specific mailbox exploits, scanner-bypass recipes, and live fleet identity. Those stay private.

---

## Asset → likely attacks → controls

| Protocol asset | Likely attacks | Controls (public-layer) |
|----------------|----------------|-------------------------|
| **Envelope schema & event vocabulary** | Malformed or oversized JSON; invented events; semantic confusion (`ask` that looks like `intro`) | Validate against public schema + local policy; hub owns event vocabulary; unknown events → `error` or drop; size caps |
| **Subject wire tag** | Tag squatting / spam floods using the public demo tag `[CATBUS]` | Production: **private** tag never published; treat tag match as routing, **not** auth; rate-limit |
| **Callsigns (`sender` / `target`)** | Impersonation of `orchestrator` or peers by copying public taxonomy names | Seat allowlist; unknown senders = liveness-only until human seats; private callsigns mapped off-bus |
| **`correlation_id`** | Replay / cross-task confusion; ID guessing | Fresh UUIDs per initiation; echo on reply; do not encode secrets in IDs; optional TTL soft-drop |
| **Mailbox / transport** | Spoofed From; unauthenticated injection; mailbox takeover | Prefer self-mail; DKIM/SPF/DMARC alignment; treat unauthenticated external From as hostile; mailbox credentials never on bus |
| **Message body / payload** | Secret/PHI/PAN/credential leakage; prompt injection via free-text fields | Sterile-bus rules ([`secure-payload.md`](secure-payload.md)); allowlisted fields; no secrets in JSON |
| **Reply / thread chain** | Forging past messages; splicing a fake parent into a trusted thread | Hash-based signing + `parent_hash` / hash-into-hash ([`signing.md`](signing.md)); private keys off-bus |
| **Protocol version pin** | Downgrade / confusion via stale or fake version claims | Poll authority `protocol-version.json` + Best Practices; human GO for breaking changes; do not trust peer-claimed pins alone |
| **Human GO gate** | Social engineering to skip GO; forged “approved” packets | GO only from human owner in-thread; valid envelope ≠ authorization |
| **Best Practices / docs authority** | Supply-chain / docs spoof if agents fetch from wrong URL | Pin canonical GitHub authority; verify TLS; prefer content hash checks in private deployments |

---

## Trust boundaries

```text
  [ public docs / schema ]     -- no trust -->   [ production fleet ]
  [ unauthenticated From ]     -- hostile -->    [ hub policy ]
  [ seated peer + DKIM OK ]    -- limited -->    [ event allowlist ]
  [ human GO in-thread ]       -- authority -->  [ consequential acts ]
  [ signing key (off-bus) ]    -- proves -->     [ message / chain integrity ]
```

Public documentation alone must never be enough to forge trusted traffic against a careful deployment.

---

## Residual risk (acknowledge)

- Email is not a high-assurance C2 channel; latency and delivery are best-effort.
- DKIM proves domain alignment, not agent intent.
- Hash-chain signing resists silent rewrite of past messages; it does not replace transport auth or human GO.
- Allowlists drift; hubs SHOULD review seating and field allowlists periodically.

Extend this table privately with: shared-mailbox ACLs, filter/label names (never publish), key custody, and incident response contacts.
