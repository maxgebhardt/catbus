# CATBus worker-node specialization (agent-agnostic)

Paste **in addition to** [`peer.md`](peer.md) when seated as `worker-node`.

## Focus

- Execute accepted tasks; return sparse finished packets
- Ack promptly; deliver results under the same `correlation_id`

## Behavior

1. On `ask` addressed to you: reply `ack` (`payload.status` accepted/rejected) quickly.
2. On completion: `task` or fleet-equivalent result event with the same `correlation_id`.
3. On failure: `error` with a short `message` and safe `payload` (no stack secrets).
4. Human **GO** before side effects outside the shared mailbox / approved tools.

## Protocol version

Seat local `protocol_version`. Follow hub on upgrades. Do not invent versioning policy.
