# Correlation IDs

CATBus ties request and response packets with an opaque `correlation_id`.

## Rules

1. **Issuer creates the ID.** The agent that sends the initiating event (e.g. `ping`, `ask`) generates a fresh `correlation_id` (prefer UUID).
2. **Responder echoes it.** The matching response (`pong`, `ack`, `task` result, `error`) must reuse the same `correlation_id`.
3. **Optional `in_reply_to`.** Responders may set `in_reply_to` to the prior event name (e.g. `"ping"`) for human skimability; correlation_id remains authoritative.
4. **TTL is soft.** `ttl_seconds` is a hint. Late packets may still be useful for audit; hubs decide whether to honor expiry.
5. **One conversation, many packets.** A long-running task may emit multiple telemetry packets sharing the original correlation_id, or mint child IDs documented in `payload` — keep the convention consistent within a fleet and private.

## Example

Request:

```json
{
  "v": "2",
  "event": "ask",
  "correlation_id": "550e8400-e29b-41d4-a716-446655440002",
  "sender": "dispatcher",
  "target": "worker-node",
  "payload": { "task": "summarize" }
}
```

Response:

```json
{
  "v": "2",
  "event": "ack",
  "correlation_id": "550e8400-e29b-41d4-a716-446655440002",
  "sender": "worker-node",
  "target": "dispatcher",
  "in_reply_to": "ask",
  "payload": { "status": "accepted" }
}
```

## Anti-patterns

- Reusing a correlation_id across unrelated tasks
- Responding without echoing the ID
- Putting secrets inside correlation_id strings

## Optional event: `protocol-check`

Use `protocol-check` when a hub or peer wants an explicit version/Best Practices comparison (also fine to fold into `heartbeat` / `telemetry`).

1. Sender includes local pin in `payload` (e.g. `local_version`, `envelope_v`, optional `bp_checked_at`).
2. Same `correlation_id` rules: issuer mints; responder echoes.
3. Response is typically `telemetry` or `ack` with `payload.remote_version`, `payload.newer` (bool), and a short human `message` — never auto-apply breaking changes.
4. If newer Best Practices exist, prefer a sparse `ask` to the human owner summarizing the delta and requesting GO for breaking upgrades.

Example request payload shape (illustrative):

```json
{
  "local_version": "0.3.0",
  "envelope_v": "2",
  "authority": "https://github.com/maxgebhardt/catbus"
}
```

