# Roles (sanitized public taxonomy)

Public docs and prompts use **only** these role names. A deployment may map private callsigns onto these roles. Do not publish live callsigns here.

Names below are examples. Seat each one only after an explicit human yes ([`00-stand-up-order.md`](00-stand-up-order.md)).

Bus traffic is self-mail on one shared mailbox (From = To). These roles do not imply separate SMTP recipients. A mailbox rule on the wire tag keeps bus mail out of the ordinary human inbox.

| Role | Responsibility |
|------|----------------|
| `orchestrator` | Hub. Owns protocol, seating, intro/role map, and GO-gate enforcement. |
| `dispatcher` | Routes tasks to workers; tracks ask/ack lifecycle. |
| `worker-node` | Executes accepted tasks; returns sparse finished packets. |
| `audit-node` | Observes bus traffic for policy/compliance notes; does not invent protocol. |

## Seating

1. Human seats the hub first — see [`00-stand-up-order.md`](00-stand-up-order.md). `orchestrator` is an example callsign, used only after an explicit yes.
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
