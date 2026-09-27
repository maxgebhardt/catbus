# Changelog

All notable changes to the public CATBus protocol and docs are documented here.

Format based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).
Versioning follows [Semantic Versioning](https://semver.org/) on `schema/protocol-version.json` (`version`), aligned to envelope major via `envelope_v`.

## [Unreleased]

### Added

- (none yet)

### Changed

- (none yet)

### Deprecated

- (none yet)

### Removed

- (none yet)

### Fixed

- (none yet)

### Security

- (none yet)

## [0.3.0] — 2026-09-27

### Added

- Asset-based threat model (`docs/threat-model.md`) — protocol assets → likely attacks → controls; links Best Practices.
- Hash-based signing guide (`docs/signing.md`) — private keys off-bus, message signing, reply-chain `parent_hash` / hash-into-hash; optional `schema/signing.schema.json`.
- Sterile-bus secure payload rules (`docs/secure-payload.md`) — no secrets/PHI/PANs/credentials on the wire; allowlisted fields; `example.com` only; `schema/secure-payload.schema.json`.

### Changed

- Stand-up order, README, Best Practices, and security docs cross-link threat model / signing / secure payload in the security path.
- `schema/protocol-version.json` pins `0.3.0` and lists new doc URLs.

### Removed

- Public branding-guidelines doc (`docs/brand.md`). README keeps amber/slate/cyan aesthetic and logo assets without a published brand guide.

### Notes

- Envelope major remains `v: "2"` (`envelope_v`).
- Public demo wire tag: `[CATBUS]`. Production fleets SHOULD use a private tag.

## [0.2.0] — 2026-09-27

### Added

- Public CATBus package: README, MIT license, assets (SVG mark/favicon, PNG mark).
- Envelope schema v2 (`schema/catbus-envelope.schema.json`) with required `v` const `"2"`.
- Protocol version pin (`schema/protocol-version.json`) and living Best Practices authority (`docs/best-practices.md`).
- Stand-up order (`docs/00-stand-up-order.md`) including periodic protocol-version / best-practices fetch.
- Architecture, security, correlation, roles docs.
- Agent-agnostic drop-in prompts: hub, peer, dispatcher, worker-node, orchestrator, audit-node.
- Examples: ping/pong, ask/ack.
- Optional public event `protocol-check` for sparse version/BP comparison telemetry.

### Notes

- Public demo wire tag: `[CATBUS]`. Production fleets SHOULD use a private tag.
- Sanitized roles only in public materials: `orchestrator`, `dispatcher`, `worker-node`, `audit-node`.
