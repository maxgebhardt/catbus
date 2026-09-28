# Security notes

## Threat we care about

An adversary who reads these public docs and tries to inject a counterfeit hub or peer into someone else's CATBus fleet.

Public documentation alone is not enough to forge trusted traffic against a careful deployment.

## Security hierarchy

| Component | Status | Doc |
|-----------|--------|-----|
| Asset-based threat model | **MUST** | [`threat-model.md`](threat-model.md) |
| Sterile cleartext payloads | Preferred baseline | [`secure-payload.md`](secure-payload.md) |
| Message signing + chain hash | **OPTIONAL**, **recommended** | [`signing.md`](signing.md) |
| Opaque secure envelope | **OPTIONAL**, **advised against** | [`secure-envelope.md`](secure-envelope.md) |

Use cleartext, reviewable payloads. Add signing when keys are seated. Do not use opaque envelopes for ordinary hub traffic. Opacity removes human review.

Living practices: [`best-practices.md`](best-practices.md).

## Transport in one paragraph

This protocol is **exclusively self-mail on one shared mailbox** (From = To = the owner mailbox). Gmail or another agent-accessible mailbox is the bus. `target` is a callsign in JSON, not an external recipient. A separate cross-hub protocol exists; this document does not specify it and is not a stand-up guide for it.

A mailbox rule on the subject wire tag keeps bus traffic out of the ordinary human inbox. The human owner still has access to review those messages.

## Public vs private surface

| Published here | Kept private (do not publish) |
|----------------|-------------------------------|
| Protocol shape and these docs | Real mailbox addresses |
| Public envelope schema (v2) | Production subject tags and filter names |
| Sanitized roles and `@example.*` domains | Live callsign lists and peer lists |
| Defender principles below | Deployment-specific filter, webhook, or job config |
| Demo wire tag `[CATBUS]` | Private fleet wire tags |
| Generic signing and sterile-payload schemas | Live key IDs, fingerprints, seating maps |
| Asset-based threat model | Event catalogs of a specific fleet |
| Secure-envelope doc, with the advise-against stance | Credentials, app passwords, tokens |
| | Scanner-bypass recipes |

## Defender principles

1. **Transport binding.** DKIM/SPF/DMARC. Bus packets are self-mail on the one shared mailbox. Treat unauthenticated external From as hostile.
2. **Wire tag plus mailbox rule.** Production subject tags must not be the public demo tag `[CATBUS]` unless the human deliberately seats that demo tag. Tag match is not authentication. The filter exists so bus mail does not bury the human inbox.
3. **Hub owns the protocol.** Unknown senders get liveness-only behavior until a human seats them.
4. **Schema is not trust.** Validate shape, then apply policy. Valid JSON is not an authorization decision.
5. **Least-privilege cleartext payloads.** No secrets, health data, payment numbers, or credentials in bus JSON ([`secure-payload.md`](secure-payload.md)).
6. **Optional hash-based signing.** Keys stay off the bus. `parent_hash` on replies resists forged history ([`signing.md`](signing.md)).
7. **Avoid opaque envelopes.** Reviewability is a control ([`secure-envelope.md`](secure-envelope.md)).
8. **Rate and volume.** Rate-limit and alert on floods.
9. **Human GO for consequential acts.** Third-party outbound mail, spends, and irreversible infrastructure changes stay behind explicit human approval. Third-party mail is not bus traffic. A verified envelope is still not that approval.
10. **Self-mail only for bus packets.** From = To = the owner mailbox. Do not address bus packets to external recipients.

## What we will not document here

Techniques whose primary value is defeating mailbox scanners, forging From headers past a specific deployment, or impersonating a named hub. Deployment-specific hardening stays in a private runbook.

## Never publish

- Live wire tags or filter-rule names
- Live callsigns or role maps of a real fleet
- Mailbox credentials or OAuth tokens
- Signing private keys or live key ceremonies
- Real addresses (public examples use `@example.com`, `@example.org`, `@example.net` only)

## Reporting

If documentation in this repository would help forge traffic against a deployment, open a GitHub issue on `github.com/maxgebhardt/catbus` marked `security`. Do not include exploit material aimed at third parties, live tags, or real addresses.
