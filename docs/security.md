# Security notes (public)

## Threat we care about

An adversary who reads these public docs and tries to **inject a counterfeit hub** (or peer) into someone else's CATBus fleet.

CATBus is designed so that **public documentation alone is not enough** to forge trusted traffic against a careful deployment.

Deep dive (asset table): [`threat-model.md`](threat-model.md).  
Signing / chain integrity: [`signing.md`](signing.md).  
Sterile payloads: [`secure-payload.md`](secure-payload.md).  
Living practices: [`best-practices.md`](best-practices.md).

## Public vs private surface

| Published here | Kept private (never publish) |
|----------------|------------------------------|
| Conceptual architecture | Real mailbox addresses |
| Public envelope schema (v2) | Production subject tags / labels |
| Sanitized roles & `@example.*` domains | Live callsign registries and peer lists |
| High-level defender principles | Exact filter / webhook / routine config |
| Demo wire tag `[CATBUS]` | Private fleet wire tags |
| Generic signing & sterile-payload schemas | Live key IDs, fingerprints, seating maps |
| Asset-based threat model (non-exhaustive) | Event catalogs of a specific fleet |
| | Credentials, app passwords, tokens |
| | Scanner-bypass or “armor” recipes |
| | Branding guidelines (not published) |

## Defender principles (non-exhaustive)

1. **Transport binding.** Prefer authenticated mail paths (DKIM/SPF alignment, self-mail or tightly allowlisted From). Treat unauthenticated external From as hostile by default.
2. **Private wire tag.** Production subject tags must not match public documentation placeholders. `[CATBUS]` is for demos/docs only.
3. **Hub is source of record.** Peers do not redefine protocol. Unknown senders get liveness-only behavior until a human seats them.
4. **Schema is not trust.** Validate shape, then apply policy. A valid JSON envelope is not an authorization decision.
5. **Least privilege payloads.** No secrets, PHI, PANs, credentials, or recovery material in bus JSON ([`secure-payload.md`](secure-payload.md)).
6. **Hash-based signing.** Private keys off-bus; sign message hashes; bind `parent_hash` into replies to resist forging past messages ([`signing.md`](signing.md)).
7. **Rate and volume.** Spam and spoof floods are operational attacks; hubs should rate-limit and alert.
8. **Human GO for consequential acts.** Outbound mail to third parties, spends, and infra changes stay behind explicit human approval even when the envelope looks right (and even when it verifies).
9. **Self-mail for machine traffic.** Prefer hub and peers sending machine packets from/to the shared mailbox rather than open internet From spoofing.

## What we will not document publicly

We will not publish techniques whose primary value is helping an attacker defeat mailbox scanners, forge From headers past a specific deployment, or impersonate a named hub. Hardening recipes for your own fleet stay in a private runbook.

## Never publish

- Live wire tags or filter rules
- Live callsigns or role maps of a real fleet
- Mailbox credentials or OAuth tokens
- Signing private keys or live key ceremonies
- Real addresses (public examples use `@example.com`, `@example.org`, `@example.net` only)

## Reporting

If you find a documentation leak that would help forge traffic against a public CATBus example, open a GitHub issue on `github.com/maxgebhardt/catbus` marked `security` with **no** exploit payload against third parties.
