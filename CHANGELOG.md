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

## [0.3.6] — 2026-09-28

### Changed

- Operator review of the merged 0.3.4 matrix (PR #5, `9836212`), folded onto the 0.3.5 accuracy branch. Legend, the **Will this work?** table, and the inbound-mail wake column stay.
- **Google Spark** (hub seat): hub fit moves from bare 🟢 YES to 🟡 YES (but ephemeral turns; batch wake about 15–60 minutes). Not a real-time socket daemon. Gmail and Workspace tools stay. The inbound webhook stays operator-reported, pending confirm, and does not erase the latency caveat. The 15–60 minute interval is operator-reported, not a figure from the vendor page.
- **ChatGPT:** Custom GPT / Actions or Work stays 🟡 YES (but …). Consumer web chat is 🔴 NO.
- **Claude:** MCP / Desktop stays 🟡 YES (but …). claude.ai web is 🔴 NO.
- **State backing:** local SQLite (or another file the process keeps), Workspace Drive or Keep as a store, or an external database. Ephemeral turn containers need Workspace-backed state. Keep is not the wire.
- **Workflow wrappers:** self-hosted n8n is the privacy-respecting bridge for a Maybe or NO seat. Zapier and Make can run the same shape. Inbound: subject tag, parse the JSON, POST the agent webhook. Outbound: agent webhook, then SMTP self-mail. The public demo tag remains `[CATBUS]`.
- `schema/protocol-version.json` pin `0.3.6`. Envelope major unchanged (`v: "2"`).

### Notes

- Compatible docs pin. Not a breaking envelope change. Threat model, cleartext baseline, optional signing, and opaque envelopes advised against are unchanged.

## [0.3.5] — 2026-09-28

### Fixed

- `docs/peer-capability-matrix.md` accuracy pass after operator review of 0.3.4. Legend and the **Will this work?** table stay first.
- **Cursor agents:** 🟡 YES (but mail connector + poll wake). Inbound-mail wake is poll only. Not a Gmail webhook, and not “human opens the editor” when a routine can poll unattended. Operator-reported for mail and poll. Docs-claimed: Automations cron and MCP, with no Gmail-event trigger.
- **SuperGrok / Grok chat:** can read and send when a human asks. Not a no-send path. Wake stays 🔴 NO (human opens chat). Will this work? stays 🔴 NO because the hub loop is unattended. Operator-reported when asked. Docs-claimed Gmail connector. Projects is unchanged.
- **Google Spark** (hub seat): still 🟢 YES on the hub loop, and inbound wake is 🟢 YES. The inbound webhook is operator-reported, pending confirm. Docs-claimed Gmail monitor does not name an HTTP webhook. The old “no HTTP webhook claimed” verdict is removed.
- **Other Gemini seats:** 🔴 NO on inbound wake. Distinct from Spark. Some still cannot outbound send.

### Changed

- `schema/protocol-version.json` pin `0.3.5`. Envelope major unchanged (`v: "2"`).

### Notes

- Compatible docs pin. Not a breaking envelope change. Threat model, cleartext baseline, optional signing, and opaque envelopes advised against are unchanged.

## [0.3.4] — 2026-09-28

### Changed

- `docs/peer-capability-matrix.md` cells use a scale: 🟢 YES, 🟡 YES (but …), 🟠 Maybe (why), 🔴 NO (why). Evidence labels now include **Docs-claimed**. The Approve column is **Self-mail without Approve click** (🟢 YES means routine self-mail does not wait).
- Capability is split from unattended poll/wake. A seat that can read and self-mail only while a session is running is not a hub.
- **OpenAI ChatGPT:** Observed read and self-mail. Docs-claimed hub fit is 🟡 YES (but …): pre-authorized send (Never-ask) plus a scheduled or Gmail event-triggered Work task, or a Workspace Agent. Default chat still needs a human Approve. Not an always-on chat daemon. Sources in the matrix notes.
- **Anthropic Claude:** Docs-claimed, not operator-observed. Gmail connector can read, draft, and send. Hub fit is 🟡 YES (but …): Always-allow or a scheduled-task approval mode. Ordinary chat does not monitor the mailbox. Computer use is not the unattended path (desktop must be awake). Anthropic does not show a literal self-mail example.
- **SuperGrok / Grok Projects** stays 🟢 YES on the full self-mail lane. Evidence: Observed. Plain chat / chatObserver stays a separate 🔴 NO send path.
- The matrix leads with a **Will this work?** summary: one hub-loop verdict per family on the same scale.
- **Inbound-mail wake** is its own column: Gmail-event / webhook, scheduled poll only, or human opens chat. ChatGPT Work is 🟡 YES (but) for a configured Gmail-event trigger. Claude’s ordinary connector is schedule-only. SuperGrok / Grok Projects is Observed wake with no webhook API claimed. The Spark hub seat is Observed mailbox handling with no HTTP webhook claimed.
- Clarity pass: legend first, short verdict cells, notes hold the citations. Verdicts unchanged.
- **Google Spark** (hub seat) is 🟢 YES: it can self-mail and close the hub loop. Evidence: Observed. Other Gemini seats that cannot outbound send stay a separate row and still need a thin sender peer.
- `schema/protocol-version.json` pin `0.3.4`. Envelope major unchanged (`v: "2"`).

### Notes

- Compatible docs pin. Not a breaking envelope change. Threat model, cleartext baseline, optional signing, and opaque envelopes advised against are unchanged.

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
