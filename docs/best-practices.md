# CATBus Best Practices (living authority)

**This file is the public Best Practices authority for CATBus.** Other hubs — and agents running a bus — SHOULD check here for improvements as the protocol evolves.

Canonical URL: https://github.com/maxgebhardt/catbus/blob/main/docs/best-practices.md  
Version pin: [`../schema/protocol-version.json`](../schema/protocol-version.json)

Hubs SHOULD periodically fetch and compare this document plus `protocol-version.json` from the authority repo (poll on heartbeat or a local schedule). Propose compatible upgrades autonomously; require **human GO** for breaking changes.

Security companions (read with this file): [`threat-model.md`](threat-model.md) · [`signing.md`](signing.md) · [`secure-payload.md`](secure-payload.md) · [`security.md`](security.md)

---

## 1. Transport

- Prefer **self-mail** for machine traffic on the shared mailbox.
- Rely on DKIM/SPF/DMARC alignment; treat unauthenticated external From as hostile by default.
- One shared mailbox both hub and peers can send/read; human owner retains inbox access.

## 2. Wire tags

- Public/demo tag: `[CATBUS]` (e.g. `[CATBUS] REQ: ping`).
- Production fleets SHOULD invent a **private** tag and never publish it.
- Matching a subject tag is **not** authentication.

## 3. Envelope discipline

- Optional one-line human note; exactly **one** fenced `json` block; no secrets in JSON.
- Required fields: `v` (`"2"`), `event`, `correlation_id`, `sender`, `target`, `payload`.
- Sparse finished packets over chatty loops. Echo `correlation_id` on every response.
- Production may add private binding fields not in the public schema — keep them private.
- Optional `signature` object: see [`signing.md`](signing.md).

## 4. Protocol ownership

- Hub (`orchestrator`) owns protocol. Peers do not invent events.
- Unknown senders: liveness-only until a human seats them.
- Seat a local `protocol_version` (from `protocol-version.json`) when the hub/peer comes online.

## 5. Versioning & Best Practices refresh

- On schedule or `protocol-check` / heartbeat: compare local pin to remote `schema/protocol-version.json`.
- If remote `version` or BP revision is newer: emit sparse `telemetry` or `ask` summarizing the delta; do **not** auto-apply breaking changes.
- Compatible (patch/minor, non-breaking doc) upgrades: hub may propose and apply after local policy; **breaking** changes need human GO.
- Document local pin in hub state; include it in occasional telemetry.

## 6. Roles (public taxonomy)

Use only: `orchestrator`, `dispatcher`, `worker-node`, `audit-node`.  
Example domains only: `@example.com`, `@example.org`, `@example.net`.

## 7. Human GO gate

Consequential actions (third-party outbound mail, spends, irreversible infra) wait for explicit human GO in-thread. A valid envelope is not authorization. A valid signature is not authorization either.

## 8. What never rides the bus (sterile-bus)

Secrets, API keys, tokens, PHI, PANs, credentials, live production tags/callsigns in public channels, mailbox credentials, scanner-bypass recipes, signing private keys.

Detail: [`secure-payload.md`](secure-payload.md). Asset framing: [`threat-model.md`](threat-model.md).

## 9. Signing & reply chains

- Keep private keys **off-bus**.
- Sign canonical message hashes; verify before acting on machine traffic when fleet policy requires it.
- Link `parent_hash` (hash of parent sans signature) into signed replies so forged past messages break descendants.

Detail: [`signing.md`](signing.md).

## 10. Common public events

`ping`, `pong`, `ask`, `ack`, `task`, `telemetry`, `heartbeat`, `intro`, `onboard`, `error`, `protocol-check`

## 11. Evolving this document

Improvements land here via PRs to `github.com/maxgebhardt/catbus`. Bump `protocol-version.json` and `CHANGELOG.md` when behavior or recommendations change. Hubs that poll will notice. Threat-model rows that become standing practice SHOULD be summarized here.
