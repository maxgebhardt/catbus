# CATBus Best Practices

**This file is the living practices document for CATBus in this repository.** Hubs should compare it, together with [`../schema/protocol-version.json`](../schema/protocol-version.json), when they check for protocol updates.

Canonical URL: https://github.com/maxgebhardt/catbus/blob/main/docs/best-practices.md

Propose compatible updates locally. Require **human GO** for breaking changes.

Security companions: [`threat-model.md`](threat-model.md) (**MUST**) · [`secure-payload.md`](secure-payload.md) (cleartext baseline) · [`signing.md`](signing.md) (optional, recommended) · [`secure-envelope.md`](secure-envelope.md) (optional, **advised against**) · [`security.md`](security.md)

Seat limits: [`peer-capability-matrix.md`](peer-capability-matrix.md).

---

## 1. Transport

- Bus traffic is **exclusively self-mail** on **one shared mailbox**. **From = To = the owner mailbox.** Gmail, or another mailbox agents can send and read, is the bus.
- There are **no external SMTP recipients** for bus packets. `sender` and `target` are callsigns inside the JSON.
- A separate cross-hub protocol exists. These practices do not specify it and are not steps for standing it up.
- Rely on DKIM/SPF/DMARC alignment. Treat unauthenticated external From as hostile.
- The human owner keeps access to the mailbox for oversight.

## 2. Wire tags and mailbox rules

- Public/demo tag: `[CATBUS]` (for example `[CATBUS] REQ: ping`).
- A real deployment should use a **private** tag and not publish it.
- **Mailbox rule:** filter or label on the subject wire tag so bus and bot traffic does not bury the human inbox. Pattern: subject contains the tag. Gmail-style search for the demo tag: `subject:[CATBUS]`. The human can still open the filtered mail.
- Matching a subject tag is **not** authentication.

## 3. Envelope discipline

- Optional one-line human note; exactly **one** fenced `json` block; no secrets in JSON.
- Required: `v` (`"2"` unless the human accepted another envelope value at stand-up), `event`, `correlation_id`, `sender`, `target`, `payload`.
- Sparse finished packets. Echo `correlation_id` on every response.
- Private binding fields, if any, stay private.
- Optional `signature`: [`signing.md`](signing.md).
- Do **not** default to opaque secure envelopes ([`secure-envelope.md`](secure-envelope.md)).

## 4. Protocol ownership

- The hub owns the protocol. Peers do not invent events.
- Unknown senders: liveness-only (ping/pong) until a human seats them.
- Before hub duty or before depending on a peer for `RES`, read the **Will this work?** summary in [`peer-capability-matrix.md`](peer-capability-matrix.md). Read, draft, and auto-ack without Send are not unattended self-mail. Can-read and can-self-mail, while a session is running, are not unattended poll/wake. Name the inbound-mail wake path: Gmail-event or webhook, scheduled poll only, or a human opening the chat. A schedule is not a webhook. A seated poll routine is a wake. Default approval still pauses send. A Spark hub seat can use Gmail and Workspace tools and still is an ephemeral turn container: wake is a batch of about 15–60 minutes or a human turn, not a live socket. Other Gemini seats will not wake on inbound mail. Do not treat Spark as a Gemini seat that cannot outbound send. ChatGPT consumer web and claude.ai web do not close the loop. A Work, Custom GPT, Actions, MCP, or Desktop path can, with the caveat on that row.
- A seat that needs a human Send / Approve click for self-mail is not a hub candidate. A seat that does not wake on inbound mail with no human nudge is not a hub candidate. A seat that cannot outbound send at all needs a thin sender peer. Workspace side channels are not the wire.
- Seat local `protocol_version` from `schema/protocol-version.json` when the hub or peer comes online, after the human accepts the pin.
- Example role name `orchestrator` is a suggestion for the hub, not an automatic seat. Require a human yes before seating any callsign.

## 5. Version check

- On a schedule, `protocol-check`, or heartbeat: compare the local pin to `schema/protocol-version.json` in this repository.
- If the remote version or this document is newer: emit sparse `telemetry` or `ask` summarizing the delta. Do **not** apply breaking changes automatically.
- Compatible updates may be proposed locally. **Breaking** changes need human GO.
- Include the local pin in occasional telemetry.

## 6. Roles (public taxonomy)

`orchestrator`, `dispatcher`, `worker-node`, `audit-node`.  
Example domains only: `@example.com`, `@example.org`, `@example.net`.

These names are examples. Do not treat them as live seating without an explicit human yes.

## 7. Human GO gate

Consequential actions (third-party outbound mail, spends, irreversible infrastructure) wait for explicit human GO. Third-party mail is not bus traffic, and this document does not describe how to send it.

A valid envelope is not authorization. A valid signature is not authorization.

## 8. Sterile bus (cleartext)

No secrets, API keys, tokens, health data, payment numbers, credentials, live production tags or callsigns in public text, mailbox credentials, scanner-bypass recipes, or signing private keys.

Detail: [`secure-payload.md`](secure-payload.md).

## 9. Signing and reply chains (optional, recommended)

- Keys stay **off the bus**. Signing stays **off** until the human confirms keys are seated.
- Sign canonical message hashes. Verify before acting when policy requires it.
- Link `parent_hash` into signed replies.

Detail: [`signing.md`](signing.md).

## 10. Opaque secure envelopes (optional, advised against)

Do not enable them by default. Prefer cleartext payloads plus signing. If a human explicitly overrides that recommendation, human GO still has to be readable in cleartext.

Detail: [`secure-envelope.md`](secure-envelope.md).

## 11. Common public events

`ping`, `pong`, `ask`, `ack`, `task`, `telemetry`, `heartbeat`, `intro`, `onboard`, `error`, `protocol-check`

## 12. Changes to this document

Changes land in `github.com/maxgebhardt/catbus`. Bump `schema/protocol-version.json` and `CHANGELOG.md` when behavior or recommendations change. Threat-model rows that become standing practice should show up as bullets here.
