# CATBus

```
 /\\  /\\     [CATBUS]
/  --  \\    Cross-Agent Tasking Bus
(  o  o  )   SMTP in. Tasks out. No broker.
 \\  ==  /
```

<p align="center">
  <img src="assets/logo-mark.svg" alt="CATBus mark" width="160" />
</p>

**Your agents need to talk. You don't need a broker. SMTP is already your message bus. You just haven't used it yet.**

**CATBus (Cross-Agent Tasking Bus)** — a serverless, asynchronous tasking and telemetry bus for distributed multi-agent fleets running on standard email infrastructure. No broker. No extra infra. SMTP in. Tasks out.

This repository is the **public authority** for the redacted envelope schema, stand-up order, Best Practices, and agent-agnostic drop-in prompts. Production deployments use private wire tags, private callsigns, and binding checks that are **not** published here.

| Pin | Value |
|-----|-------|
| Protocol | `catbus` **0.3.0** ([`schema/protocol-version.json`](schema/protocol-version.json)) |
| Envelope | `v: "2"` |
| License | MIT |
| Repo | [`github.com/maxgebhardt/catbus`](https://github.com/maxgebhardt/catbus) |

## Why email

- **Async by default.** Agents do not share memory or block on each other's sockets.
- **Human-in-the-loop is native.** Oversight is an inbox thread, not a custom admin UI.
- **Transport already solved.** Delivery retries, threading, search, and domain authentication (DKIM / SPF / DMARC) come with the mail rails.
- **Heterogeneous compute.** Local nodes, cloud coding agents, enterprise copilots, and Workspace automation exchange structured envelopes without sharing a network plane.

## Stand up a hub

Start here: **[`docs/00-stand-up-order.md`](docs/00-stand-up-order.md)** — numbered steps from zero (mailbox → filter → ping/pong → protocol pin → security path → peers → intro → GO gate).

Living improvements: **[`docs/best-practices.md`](docs/best-practices.md)** (hubs SHOULD poll this + `protocol-version.json`).

Security path (read during stand-up): [`docs/threat-model.md`](docs/threat-model.md) → [`docs/signing.md`](docs/signing.md) → [`docs/secure-payload.md`](docs/secure-payload.md) → [`docs/security.md`](docs/security.md).

## What's in this repo

| Path | Purpose |
|------|---------|
| [`docs/00-stand-up-order.md`](docs/00-stand-up-order.md) | Ordered stand-up from zero |
| [`docs/best-practices.md`](docs/best-practices.md) | Living Best Practices authority |
| [`docs/architecture.md`](docs/architecture.md) | Topology and design principles |
| [`docs/threat-model.md`](docs/threat-model.md) | Asset-based threat model (non-exhaustive) |
| [`docs/signing.md`](docs/signing.md) | Hash-based signing + reply-chain linking |
| [`docs/secure-payload.md`](docs/secure-payload.md) | Sterile-bus payload rules |
| [`docs/security.md`](docs/security.md) | Public vs private; defender principles |
| [`docs/correlation.md`](docs/correlation.md) | Correlation IDs + `protocol-check` |
| [`docs/roles.md`](docs/roles.md) | Sanitized public role taxonomy |
| [`schema/catbus-envelope.schema.json`](schema/catbus-envelope.schema.json) | Draft-07 envelope schema (v2) |
| [`schema/signing.schema.json`](schema/signing.schema.json) | Optional signature object schema |
| [`schema/secure-payload.schema.json`](schema/secure-payload.schema.json) | Sterile payload field patterns |
| [`schema/protocol-version.json`](schema/protocol-version.json) | Semver protocol pin |
| [`prompts/`](prompts/) | Agent-agnostic markdown drop-ins |
| [`examples/`](examples/) | ping/pong, ask/ack samples |
| [`CHANGELOG.md`](CHANGELOG.md) | Keep a Changelog |

## Envelope (public v2)

Minimum fields:

```json
{
  "v": "2",
  "event": "ping",
  "correlation_id": "550e8400-e29b-41d4-a716-446655440001",
  "sender": "orchestrator",
  "target": "worker-node",
  "payload": {}
}
```

Body placement: optional one-line human note; exactly one fenced `json` block; no secrets in JSON.

Common public events: `ping`, `pong`, `ask`, `ack`, `task`, `telemetry`, `heartbeat`, `intro`, `onboard`, `error`, `protocol-check`.

Production may add private binding fields not defined in the public schema. Optional signing attachment: see [`docs/signing.md`](docs/signing.md).

## Subject line (illustrative)

```text
[CATBUS] REQ: ping
[CATBUS] RES: pong
```

Treat `[CATBUS]` as the **public/demo** tag. High-security fleets SHOULD choose a private subject tag and must not assume matching this pattern is authentication.

## Sanitized public taxonomy

| Roles | Domains |
|-------|---------|
| `orchestrator`, `dispatcher`, `worker-node`, `audit-node` | `@example.com`, `@example.org`, `@example.net` |

## Protocol versioning

Hubs SHOULD periodically fetch/compare [`schema/protocol-version.json`](schema/protocol-version.json) and [`docs/best-practices.md`](docs/best-practices.md) from this authority repo (on heartbeat, schedule, or `protocol-check`). Propose compatible upgrades autonomously; **human GO** for breaking changes.

## What this repo will never publish

- Real mailbox addresses or account identifiers
- Production subject tags or filter rules
- Live callsign registries or hub peer lists
- Event catalogs of a specific fleet
- Scanner-bypass techniques
- Credentials, app passwords, webhook URLs, or routine IDs
- Branding guidelines (visual aesthetic stays in README/`assets/` only)

See [`docs/security.md`](docs/security.md) and [`docs/threat-model.md`](docs/threat-model.md).

## Status

Public reference **0.3.0** — redacted for open publication. Schema evolves under semver on `protocol-version.json`; envelope major on field `v`.

## License

MIT — see [`LICENSE`](LICENSE).
