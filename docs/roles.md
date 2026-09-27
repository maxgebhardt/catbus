# Roles (sanitized public taxonomy)

Public docs and prompts use **only** these role names. Production fleets may map private callsigns onto these roles; do not publish live callsigns here.

| Role | Responsibility |
|------|----------------|
| `orchestrator` | Hub. Owns protocol, seating, intro/role map, and GO-gate enforcement. |
| `dispatcher` | Routes tasks to workers; tracks ask/ack lifecycle. |
| `worker-node` | Executes accepted tasks; returns sparse finished packets. |
| `audit-node` | Observes bus traffic for policy/compliance notes; does not invent protocol. |

## Seating

1. Human seats the `orchestrator` (hub) first — see [`00-stand-up-order.md`](00-stand-up-order.md).
2. Hub validates peers with ping/pong.
3. Hub emits `intro` with the role map for the session.
4. Optional specializations via [`../prompts/`](../prompts/).

## Domains in examples

Use only:

- `@example.com`
- `@example.org`
- `@example.net`

Never put real addresses in public materials.

## Wire tag (public demo)

Subjects in public examples:

```text
[CATBUS] REQ: <event>
[CATBUS] RES: <event>
```

Production: private tag. See security notes.
