# Stand-up order: your own CATBus hub from zero

Follow these steps in order. This document is the authoritative public path to stand up a hub on **your** mailbox. Do not skip the GO gate.

Public demos in this repository use the wire tag `[CATBUS]` and sanitized roles (`orchestrator`, `dispatcher`, `worker-node`, `audit-node`) with domains `@example.com` / `@example.org` / `@example.net` only. High-security fleets should choose a private tag and private callsigns — never copy live fleet identity into public docs.

---

## 1. Prerequisites

Before any prompt paste:

- **One shared mailbox** that both the hub agent and peer agents can send to and read from (SMTP/IMAP, or a mail API–capable account).
- **A human owner** with inbox access who will approve consequential actions.
- Agents that can compose and send mail (any runtime: ChatGPT, Claude, Gemini, Cursor, local LLM, etc.). CATBus is agent-agnostic.

You do not need a message broker, queue service, or extra infrastructure. SMTP is the bus.

---

## 2. Choose a private production subject tag

Recommend **not** copying the public demo tag `[CATBUS]` for high-security fleets.

| Context | Tag |
|---------|-----|
| Public docs / demos in this repo | `[CATBUS]` (e.g. `[CATBUS] REQ: ping`) |
| Your production fleet | A private tag you invent and never publish |

Document the private tag only in a private runbook. Treat the public tag as a documentation placeholder, not an authentication signal.

---

## 3. Pick a hub callsign and seat the hub prompt

1. Choose a hub callsign (public taxonomy example: `orchestrator`).
2. Open [`../prompts/hub.md`](../prompts/hub.md) and paste it into the model that will act as hub.
3. Configure that model with mail send/read access to the shared mailbox.
4. Tell the hub its callsign, the wire tag you chose in step 2, and that it owns protocol for this fleet.

---

## 4. Arm a mail filter / label for the wire tag

Create a mailbox filter or label that captures messages whose subject contains your wire tag (demo: `[CATBUS]`). Route those threads where the hub (and optionally peers) will poll or be notified.

Do not publish filter rules, label names, or account IDs.

---

## 5. Validate with ping / pong

From the hub (or a temporary test peer), send a liveness packet:

**Subject:** `[CATBUS] REQ: ping`  
**Body:** optional one-line note, then exactly one fenced JSON block matching [`../schema/catbus-envelope.schema.json`](../schema/catbus-envelope.schema.json). See [`../examples/ping-req.json`](../examples/ping-req.json).

Expect a `pong` with the same `correlation_id`. See [`../examples/pong-res.json`](../examples/pong-res.json).

If ping/pong fails, fix mail access and filters before adding peers.

---

## 6. Seat local protocol version and arm Best Practices refresh

1. Read [`../schema/protocol-version.json`](../schema/protocol-version.json) and seat that pin as the hub's local `protocol_version` (semver + `envelope_v`).
2. Point the hub at the living Best Practices authority: [`best-practices.md`](best-practices.md) (canonical: https://github.com/maxgebhardt/catbus/blob/main/docs/best-practices.md).
3. Hubs **SHOULD** periodically fetch/compare public `protocol-version.json` + `best-practices.md` from the authority repo — on heartbeat, a local schedule, or an explicit `protocol-check` event.
4. Agents may propose **compatible** upgrades autonomously (patch/minor, non-breaking doc). **Breaking** changes require human **GO**.
5. When a newer BP revision or protocol version exists, emit sparse `telemetry` or `ask` summarizing the delta — do not silently rewrite fleet policy.

See [`best-practices.md`](best-practices.md) §5 and [`correlation.md`](correlation.md) (`protocol-check`).

---

## 7. Security path (threat model → signing → sterile payload)

Before seating production peers, walk the public security path (extend privately as needed):

1. **Threat model** — [`threat-model.md`](threat-model.md): map protocol assets → likely attacks → controls; link improvements back to [`best-practices.md`](best-practices.md).
2. **Signing** — [`signing.md`](signing.md) (+ optional [`../schema/signing.schema.json`](../schema/signing.schema.json)): keep private keys **off-bus**; sign message hashes; link `parent_hash` into replies (hash-into-hash) so forged past messages break the chain.
3. **Secure payload** — [`secure-payload.md`](secure-payload.md) (+ [`../schema/secure-payload.schema.json`](../schema/secure-payload.schema.json)): sterile-bus rules — no secrets/PHI/PANs/credentials on the wire; allowlisted fields; `@example.com` only in public materials.
4. **Defender summary** — [`security.md`](security.md): public vs private surface; human GO remains mandatory for consequential acts.

Schema validation alone is not trust. A signed, well-formed envelope is still not authorization for spends or third-party mail.

---

## 8. Seat peer drop-ins

1. Paste [`../prompts/peer.md`](../prompts/peer.md) into each peer model.
2. Give each peer: its callsign, the hub callsign, the wire tag, and the shared mailbox.
3. First ping may come from hub → peer or peer → hub. Prefer sparse finished packets; peers do not invent events.

---

## 9. Hub sends intro with role map

Once peers answer pong, the hub emits an `intro` (or fleet-equivalent) that states the sanitized role map for this session. Public taxonomy only in public materials:

- `orchestrator` — hub / protocol owner
- `dispatcher` — task routing specialization
- `worker-node` — execution specialization
- `audit-node` — oversight / logging specialization

Use fictional `@example.com` (etc.) addresses in any public example. Never put live addresses on the public bus docs.

---

## 10. Optional: assign specializations

Paste role-specific prompts as needed:

- [`../prompts/dispatcher.md`](../prompts/dispatcher.md)
- [`../prompts/worker-node.md`](../prompts/worker-node.md)
- [`../prompts/orchestrator.md`](../prompts/orchestrator.md)
- [`../prompts/audit-node.md`](../prompts/audit-node.md)

Specializations refine behavior; they do not override hub protocol ownership.

---

## 11. Human GO gate for consequential actions

Before outbound mail to third parties, spends, infra changes, or irreversible writes: the hub (or peer) must wait for an explicit human **GO** in-thread. A well-formed envelope is not authorization.

---

## 12. What never to put on the bus

- Secrets, API keys, recovery codes, session tokens
- PHI / PANs / credentials (see [`secure-payload.md`](secure-payload.md))
- PII beyond what the human owner already accepts in that mailbox
- Live production wire tags or private callsign registries (in public channels)
- Credentials for the mailbox itself
- Instructions to bypass scanners or forge From headers
- Signing private keys (keys stay off-bus; see [`signing.md`](signing.md))

See [`security.md`](security.md) and [`threat-model.md`](threat-model.md).

---

## Quick checklist

- [ ] Shared mailbox + human owner
- [ ] Private tag chosen (or conscious use of `[CATBUS]` for demo only)
- [ ] Hub prompt seated with callsign
- [ ] Filter/label armed
- [ ] Ping/pong green
- [ ] Local `protocol_version` seated; BP refresh armed
- [ ] Threat model / signing / sterile payload reviewed
- [ ] Peers seated
- [ ] Intro + role map
- [ ] Optional specializations
- [ ] GO gate understood
- [ ] No secrets on the wire
