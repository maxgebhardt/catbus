# CATBus architecture (public)

**CATBus (Cross-Agent Tasking Bus)** — SMTP as a task bus. No broker. No extra infra.

## Idea

Use an ordinary mailbox as an **asynchronous, human-auditable** coordination plane for heterogeneous AI agents. Agents do not open persistent sockets to each other. They drop structured JSON envelopes into email bodies. Humans can read the same threads on a phone.

Your agents need to talk. You don't need a broker. SMTP is already your message bus. You just haven't used it yet.

## Topology (conceptual)

```text
  [ worker-node ] --------\
                           \
  [ dispatcher ] -----------+-->  mailbox (async bus)  <--+-- human inbox oversight
                           /                              |
  [ audit-node ] ---------/                               |
                                                          |
  [ orchestrator / hub ] --------------------------------/
```

- **Hub (`orchestrator`)** owns protocol and semantic routing for the fleet.
- **Peers** emit and consume envelopes; they do not invent events.
- **Humans** remain first-class: GO / HOLD replies in-thread are valid control signals.

Nothing here requires a specific vendor mailbox. Gmail API is one workable path; any RFC 822 stack with DKIM/SPF can serve the same pattern.

## Envelope placement

1. Optional one-line human note (parsers ignore it).
2. Exactly one fenced JSON block matching [`../schema/catbus-envelope.schema.json`](../schema/catbus-envelope.schema.json).
3. No second fence. Prefer plain-text bodies. No secrets in JSON.

Example subject (public demo tag):

```text
[CATBUS] REQ: ping
[CATBUS] RES: pong
```

Production fleets should pick a **private** subject tag. Do not treat `[CATBUS]` as authentication.

## Common public events

| Event | Typical use |
|-------|-------------|
| `ping` / `pong` | Liveness |
| `ask` / `ack` | Task request / accept |
| `task` | Task payload or result |
| `telemetry` | Sparse status metrics |
| `heartbeat` | Periodic presence |
| `intro` | Role map / seating announcement |
| `onboard` | Peer join handshake (policy-gated) |
| `error` | Structured failure |
| `protocol-check` | Compare local vs authority `protocol-version.json` / Best Practices |

Event names are kebab-case. Production may define private events; do not publish a live fleet catalog here.

## Correlation

Every REQ/RES pair shares a `correlation_id`. See [`correlation.md`](correlation.md).

## Protocol versioning

Public pin: [`../schema/protocol-version.json`](../schema/protocol-version.json) (semver, aligned to envelope `v`).  
Living practices: [`best-practices.md`](best-practices.md).

Hubs SHOULD poll the authority repo on heartbeat or schedule, or handle an explicit `protocol-check` event. Compatible upgrades may be proposed autonomously; breaking changes need human GO. See also [`correlation.md`](correlation.md).

## Design principles

1. **Capability ≠ authority.** A valid envelope is not trust.
2. **Finished packets over chatty loops.** Sparse REQ/RES with correlation IDs.
3. **Human auditability.** If a human cannot skim the thread, the bus has failed.
4. **Decouple compute.** Nodes need not share IPs, APIs, or memory.
5. **Hub owns protocol.** Peers follow; they do not extend the event vocabulary unilaterally.
6. **Private wire stays private.** Tags, callsigns, and binding checks live outside this repo.

## Optional integrity

Fleets MAY adopt hash-based signing and reply-chain linking ([`signing.md`](signing.md)). Signing complements transport auth; it does not replace DKIM/SPF/DMARC or human GO.

## Non-goals (public scope)

- Mandatory cryptographic peer identity for all deployments (optional per [`signing.md`](signing.md))
- Guaranteed sub-second latency
- Replacement for high-throughput brokers (Kafka, etc.) where those are already justified
- Publishing live fleet keys, tags, or branding guidelines
