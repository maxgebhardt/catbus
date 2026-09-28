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

## [0.3.3] — 2026-09-28

### Changed

- `docs/peer-capability-matrix.md`: **SuperGrok / Grok Projects** can read, draft, auto-ack without Send, and send self-mail unattended. Needs human Approve for self-mail: No. Hub fit: Yes. Evidence: Observed. External mail unattended is a product capability, not a bus requirement. Bus packets stay self-mail on one shared mailbox.
- Plain **SuperGrok / Grok chat** (chat / chatObserver) stays a separate observer row when that path has no mailbox send.
- README fleet gate matches that split. Copilot and Alexa remain the named human-Send examples.
- `schema/protocol-version.json` pin `0.3.3`. Envelope major unchanged (`v: "2"`).

### Notes

- Compatible docs pin. Not a breaking envelope change. Threat model, cleartext baseline, optional signing, and opaque envelopes advised against are unchanged.

## [0.3.2] — 2026-09-28

### Changed

- Expanded `docs/peer-capability-matrix.md`: read unattended, draft, auto-ack without Send, unattended self-mail, human Approve for self-mail, and hub fit.
- Microsoft Copilot: often reads and auto-acks, and still cannot reply or send until a human clicks Send, including self-mail to the shared mailbox.
- Amazon Alexa / Alexa+: same send gate in spirit. A spoken or in-app acknowledgement is not a bus `RES`. Approve/send is required even for self-mail.
- Google Gemini / Spark-class: some seats cannot outbound send at all and need a thin sender peer. Workspace side channels (Keep, Tasks, Reminders, and similar) are not the wire.
- Stand-up, FAQ, hub prompt, and Best Practices point at that matrix before seating.
- `schema/protocol-version.json` pin `0.3.2`. Envelope major unchanged (`v: "2"`).

### Notes

- Compatible docs pin. Not a breaking envelope change. Threat model, cleartext baseline, optional signing, and opaque envelopes advised against are unchanged.

## [0.3.1] — 2026-09-28

### Added

- Opaque-envelope note (`docs/secure-envelope.md`): optional, and advised against. Readable JSON plus optional signing stays the path.
- FAQ (`docs/faq-anti-patterns.md`) and peer send matrix (`docs/peer-capability-matrix.md`).
- Hub contract for an unseated agent (`prompts/hub.md`) and interview stand-up (`docs/00-stand-up-order.md`): mint gate, topology, self-mail, mailbox rule.

### Changed

- Threat model, security notes, signing, sterile-payload rules, and Best Practices state one hierarchy: threat model required, cleartext payload baseline, signing optional and recommended, opaque envelopes optional and advised against.
- Transport text across the public docs: bus traffic is self-mail on one shared mailbox (From = To). A mailbox rule on the subject wire tag keeps bus traffic out of the ordinary human inbox.
- `schema/protocol-version.json` pin `0.3.1` plus `secure_envelope_url`.

### Security

- Opaque secure envelopes are optional and **advised against**. They are not a stronger default. Human review of cleartext packets remains in place, including human GO.

## [0.3.0] — 2026-09-27

### Added

- Asset-based threat model (`docs/threat-model.md`) — protocol assets → likely attacks → controls; links Best Practices.
- Hash-based signing guide (`docs/signing.md`) — private keys off-bus, message signing, reply-chain `parent_hash` / hash-into-hash; optional `schema/signing.schema.json`.
- Sterile-bus secure payload rules (`docs/secure-payload.md`) — no secrets/PHI/PANs/credentials on the wire; allowlisted fields; `example.com` only; `schema/secure-payload.schema.json`.

### Changed

- Stand-up order, README, Best Practices, and security docs cross-link threat model / signing / secure payload in the security path.
- `schema/protocol-version.json` pins `0.3.0` and lists new doc URLs.

### Removed

- Removed a non-protocol notes file from the public tree. SVG marks under `assets/` remain.

### Notes

- Envelope major remains `v: "2"` (`envelope_v`).
- Public demo wire tag: `[CATBUS]`. Production fleets SHOULD use a private tag.

## [0.2.0] — 2026-09-27

### Added

- Public CATBus package: README, MIT license, assets (SVG mark/favicon, PNG mark).
- Envelope schema v2 (`schema/catbus-envelope.schema.json`) with required `v` const `"2"`.
- Protocol version pin (`schema/protocol-version.json`) and living Best Practices (`docs/best-practices.md`).
- Stand-up order (`docs/00-stand-up-order.md`) including periodic protocol-version / best-practices fetch.
- Architecture, security, correlation, roles docs.
- Agent-agnostic drop-in prompts: hub, peer, dispatcher, worker-node, orchestrator, audit-node.
- Examples: ping/pong, ask/ack.
- Optional public event `protocol-check` for sparse version/BP comparison telemetry.

### Notes

- Public demo wire tag: `[CATBUS]`. Production fleets SHOULD use a private tag.
- Sanitized roles only in public materials: `orchestrator`, `dispatcher`, `worker-node`, `audit-node`.
