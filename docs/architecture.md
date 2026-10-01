# CATBus architecture

**CATBus (Cross-Agent Transfer Bus)** uses one mailbox as a task bus. No broker. No extra infrastructure. Documents in this repository are free to use under the MIT license.

## Idea

Agents do not open persistent sockets to each other. They put structured JSON envelopes in email bodies on one shared mailbox. A human can read the same threads.

## Transport

**Exclusively self-mail on that one mailbox.** From = To = the owner mailbox. Gmail, or any other mailbox the agents can send and read, can serve. There are no external SMTP recipients for bus packets. `target` is a callsign in the JSON.

A separate cross-hub protocol exists. This page does not specify it and is not a stand-up for it.

A mailbox rule on the subject wire tag keeps bus traffic out of the ordinary human inbox. The human can still open those messages. See [`00-stand-up-order.md`](00-stand-up-order.md).

## Topology (conceptual)

```text
  [ worker-node ] --------\
                           \
  [ dispatcher ] -----------+-->  one shared mailbox   <--+-- human reviews filtered mail
                           /     (self-mail bus)          |
  [ audit-node ] ---------/                               |
                                                          |
  [ orchestrator / hub ] --------------------------------/
```

Every node reads and writes the **same** mailbox. Bus packets are not addressed to a different inbox.

- **Hub** owns the protocol and callsign routing. `orchestrator` is the public example name. Seating it requires a human yes.
- **Peers** emit and consume envelopes. They do not invent events.
- **Humans** answer when the hub asks for GO. Those replies are control signals. They are not a second bus.

## Security hierarchy

1. Threat model — required ([`threat-model.md`](threat-model.md))
2. Cleartext sterile payload — baseline ([`secure-payload.md`](secure-payload.md))
3. Signing + reply-chain hash — optional, recommended ([`signing.md`](signing.md))
4. Opaque secure envelope — optional, advised against ([`secure-envelope.md`](secure-envelope.md))

## Envelope placement

1. Optional one-line human note (parsers ignore it).
2. Exactly one fenced JSON block matching [`../schema/catbus-envelope.schema.json`](../schema/catbus-envelope.schema.json).
3. No second fence. Prefer plain-text bodies. No secrets in JSON.

Example subject (public demo tag):

```text
[CATBUS] REQ: ping
[CATBUS] RES: pong
```

A deployment should pick a **private** subject tag and not publish it. Do not treat `[CATBUS]` as authentication. The tag is also the mailbox-rule pattern that keeps bus mail out of the ordinary inbox.

## Common public events

| Event | Typical use |
|-------|-------------|
| `ping` / `pong` | Liveness |
| `ask` / `ack` | Task request / accept |
| `task` | Task payload or result |
| `telemetry` | Sparse status metrics |
| `heartbeat` | Periodic presence |
| `intro` | Role map / seating announcement |
| `onboard` | Peer join (policy-gated) |
| `error` | Structured failure |
| `protocol-check` | Compare the local pin with `protocol-version.json` and Best Practices in this repository |

Event names are kebab-case. Production may define private events; do not publish a live fleet catalog here.

## Correlation

Every REQ/RES pair shares a `correlation_id`. See [`correlation.md`](correlation.md).

## Protocol versioning

Public pin: [`../schema/protocol-version.json`](../schema/protocol-version.json) (semver, aligned to envelope `v`).  
Living practices: [`best-practices.md`](best-practices.md).

Hubs should compare `schema/protocol-version.json` and [`best-practices.md`](best-practices.md) in this repository on heartbeat, on a schedule, or on `protocol-check`. Compatible updates may be proposed locally. Breaking changes need human GO. See also [`correlation.md`](correlation.md).

## Design principles

1. **Capability ≠ authority.** A valid envelope is not trust.
2. **Finished packets over chatty loops.** Sparse REQ/RES with correlation IDs.
3. **Human auditability.** If a human cannot skim the thread, the bus has failed.
4. **Decouple compute.** Nodes need not share IPs, APIs, or memory.
5. **Hub owns protocol.** Peers follow; they do not extend the event vocabulary unilaterally.
6. **Private wire stays private.** Tags, callsigns, and binding checks live outside this repo.

## Optional integrity

Deployments may add hash-based signing and reply-chain linking ([`signing.md`](signing.md)). Signing complements DKIM/SPF/DMARC. It does not replace them or human GO. Opaque envelopes are optional and advised against ([`secure-envelope.md`](secure-envelope.md)).

## Non-goals

- Mandatory signatures for every deployment (optional per [`signing.md`](signing.md))
- Cross-hub routing (a separate protocol exists; it is not specified here)
- External SMTP recipients for bus packets
- Guaranteed sub-second latency
- A replacement for a high-throughput broker where one is already in use
- Publishing live fleet keys or tags
