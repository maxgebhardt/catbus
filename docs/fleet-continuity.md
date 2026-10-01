# Fleet continuity: when half your fleet goes down

> **Status: design draft.** This describes direction, not a shipped feature. Nothing here changes the v2 envelope. Fields, values and events marked *proposed* are not in the public schema or payload allowlist yet.

Half your agents just went dark. Maybe a vendor had an outage, maybe a seat ran out of tokens, maybe you're moving a job to a different assistant on purpose. Three questions come up right away:

1. **What was in flight?** Which jobs were open, who owned them, and how far along were they?
2. **Who picks it up?** Which agent that's still running can take each job, and does it have the skills and budget for it?
3. **How does the new owner catch up?** How does it learn what the old owner knew without a human re-explaining everything?

Most multi-agent setups answer these by hand, because each agent's role, memory and instructions live inside that agent. When the agent goes, so does everything it knew. Moving a role becomes a rebuild.

CATBus takes a different position: **jobs and roles belong to the fleet, not to any one agent.** An agent is the current owner of a role. When it goes away, the role and its history stay put and get a new owner.

That rests on five parts, plus one idea underneath all of them: what memory is.

> **Scope.** Everything here runs on the normal CATBus transport: self-mail on one shared mailbox, inside one fleet. Handing context to *another person's* fleet needs a cross-hub protocol, which this repository does not specify. Treat cross-fleet handoff as direction, not as something this spec covers today.

## No hub in the infrastructure, a hub in coordination

CATBus needs no broker, server or special infrastructure. The transport is ordinary and swappable: a mailbox today, and where an agent can't send mail, a shared folder or a human carrying the packet across still works. What doesn't change is the coordination. There is a hub: a protocol owner, a role registry, a boot point, and a record that wins disagreements. The hub is a **role** (`orchestrator` in [`roles.md`](roles.md)), not a machine, so it gets re-seated like any other role. If the hub itself is down, the human runs the recovery runbook.

## 1. Distributed agents

Agents come from different vendors and run in different places, with different skills, limits and uptime. Plan for any of them to be unavailable at any time.

- Each agent holds one or more **roles** (see [`roles.md`](roles.md)). A role is a job description, not an agent identity.
- Any role can be **re-seated** on another agent that has the needed capabilities ([`peer-capability-matrix.md`](peer-capability-matrix.md) shows what each kind of seat can actually do).
- No agent is the only place a job's state lives.

## 2. Distributed messaging and state store

Different kinds of context belong in different places. Splitting them is what lets the fleet survive a partial outage.

| Store | What lives there | Typical home |
|---|---|---|
| **Message bus** | Things in motion: asks, acks, handoffs, status, alerts | A shared mailbox (CATBus's wire) |
| **Record store** | Things that last: the boot point, canon, role registry, rules, state cards, model and seat inventory, decisions | A version-controlled repository |
| **Agent-local** | Working scratch for the current session | Inside each agent; disposable |

Each store helps rebuild the other:

- **The repository is unreachable:** recent bus traffic tells every agent what was open, who asked for what, and the last known state. Agents keep working on reversible items and hold gated actions and record writes until the repository is back.
- **The mailbox is unreachable:** the repository still says who holds which role and what each role owns. Agents tell a human what they were doing and hold until the bus is back.
- **An agent is gone:** its role entry and its threads are still in the two stores. A new owner loads both.

**Rule:** context rebuilt from the bus is marked *unconfirmed* until it's checked against the record store. A stale thread must never quietly overwrite the record.

## 3. Standard formats, and checking what's real

Recovery only works if any agent can read any other agent's output, and can tell a fact from a guess.

- **One envelope.** Every message uses the CATBus v2 envelope ([`architecture.md`](architecture.md)), and a `correlation_id` ties a job's messages together ([`correlation.md`](correlation.md)). The correlation thread is the job's audit trail and fallback evidence. It is not the job's memory.
- **Bounded state cards** *(proposed).* Each job or role has a short, structured state card in the record store: owner, status, last action, open items, next step, source and date. State cards are the governed context a new owner loads. Keep them small and partitioned. A single growing memory file eventually becomes too big for some agents' tools to read, so roll older entries into an archive.
- **Provenance on everything.** Each entry says who wrote it, when, and whether a human or a model wrote it. Model-written entries are labelled as such and never signed as a human.
- **Hosts fill headers.** Models are unreliable at timestamps, sequence numbers, hashes and ids. Those fields, including a new `correlation_id`, are filled by tooling or a human, never invented by the model.
- **Optional integrity.** Where tooling allows, hash or sign records so they can be verified ([`signing.md`](signing.md)).
- **Real means confirmed.** A state is real when it's in the record store with provenance, or confirmed by the system it describes. A status an agent reports from memory is a claim, not a fact. When sources disagree, ratified canon wins over the record, and the record wins over the bus, until a human resolves it.

## 4. Governance

Shared context is shared exposure. Whoever can edit a shared boot file steers every agent that loads it, so memory poisoning, silent drift and stale facts are the main threats ([`threat-model.md`](threat-model.md)).

- **Canon is the only source of instructions.** The root rules are part of canon (below). Changes that models propose to canon, boot, rules or role files are rejected by default until a human accepts them.
- **Two-person change control** for shared boot files and role definitions: a human plus a second reviewer.
- **Need-to-know loading.** Each role loads only the context it needs. Nothing is broadcast "just in case".
- **Data class on every file**, and a list of what never enters the shared stores (secrets, credentials, regulated personal data).
- **Correct and withdraw.** Any shared entry can be corrected or withdrawn, and the change is logged.
- **Expiry.** Entries carry a date and a source, so they can be re-checked or retired.
- **Human-gated actions.** Anything that leaves the fleet, such as mail to others, posts or purchases, still needs a human GO. Losing an agent must never remove a gate.
- **Scope grows on evidence.** A role gets more authority only after logged runs, not self-reports.

## 5. Load the right context for the right agent

There is **one boot point** for the whole fleet: a single entry document in the record store. A human points any new or replacement agent at it. From there the agent learns:

1. who it is and which role it holds (from the role registry),
2. the canon every agent loads first,
3. which documents that role loads (different roles load different sets, but everything lives in the same repository),
4. which rules and gates apply,
5. which threads on the bus are its own.

The boot point is an index, not a dump. It points to the role's files rather than holding them. That keeps each agent's load small and keeps a role's context in one place no matter who holds it.

## What memory is: canon first

Memory is not everything that happened. At its heart is a short set of plain truths about what is and isn't: **canon**. Everything else is built on canon and ranks below it.

| Rank | Layer | What it is |
|---|---|---|
| 1 | **Canon** | Ratified statements of fact and the root rules. Small, dated, sourced, and changed only through review. |
| 2 | **Corollaries** | What follows directly from canon. Written down so agents don't re-derive it differently. |
| 3 | **Guidelines** | How a role should usually act. They bend to fit a situation; canon doesn't. |
| 4 | **Lessons learned** | What happened and what it taught. A lesson can *propose* a change to canon or a guideline, but only review promotes it. |

**Memory happens in front of the user.** A human decides what enters canon, what counts as ground truth, and when something hardens into it. Memory you can read, edit and ratify beats memory that's done to you. A memory a model wrote by itself, and that you can't see, is a quiet source of drift: one wrong guess about you gets stored and reused as fact. In CATBus, anything a model proposes for canon, boot, rules or role files stays a proposal until a person ratifies it.

That gate covers canon and its neighbours, not everyday traffic. State cards and bus packets are written by agents with provenance and checked against reality as described above. Requiring a human for every one would stall the fleet.

**Governed context, not transcripts.** CATBus carries pointers to canon, state cards and decisions, not conversation logs. A handoff that carries a whole transcript just moves the reconstruction work to the receiver. If what moves between agents is getting bigger rather than sharper, something is wrong. Threads are kept as audit trail and fallback evidence (see open questions on retention), but they are not what a new owner loads.

**The measure.** The best test of a handoff is how much the receiver still has to rebuild. Count it at the reconcile step, from the host's records rather than the model's own report: (a) the open items the receiver had to mark unconfirmed or rebuild, and (b) the clarifying asks it sent back to the sender or a human before its first correct action. Lower is better. It measures the quality of the packet and the record, not the receiving model.

## Recovery runbook (sketch)

**Before you need it:** the orchestrator watches heartbeats, and the miss threshold for each role lives in the private registry. Bus threads are kept long enough to recover from (an open question below); step 3 depends on it.

1. **Detect.** The orchestrator sees an agent miss its heartbeats, error out, or report that it's out of budget. If the downed agent *is* the orchestrator, the human runs the rest of this runbook.
2. **Fence the old owner.** Mark the old seat *demoted* in the registry. Until it's re-seated, its traffic is liveness-only, the same rule as for an unknown sender ([`best-practices.md`](best-practices.md) §4). Freeze its gates: nothing it had pending goes out without a human re-confirming it. Seats that ran out of budget or hit a vendor outage often come back on their own, and scheduled jobs keep firing, so on waking a demoted agent re-boots from the boot point before it acts.
3. **Inventory.** List the role's open threads from the bus, using the registry's callsign-to-role mapping as it stood at the time, and its state cards from the record store. Mark anything found only on the bus as unconfirmed.
4. **Pick a new owner.** Choose by capability and remaining budget (see below). If budget can't be compared, a human picks. A human approves re-seating for any role that holds a human-gated action.
5. **Seat the new owner.** A brand-new agent is an unknown sender until a human seats it: ping/pong, a human yes, and a hub `intro`. Then write a *provisional* registry entry with the role, the new owner, `handoff-pending`, the handoff's `correlation_id` and who approved it.
6. **Hand off.** Send a handoff packet (below) to the new owner on the bus. The new owner boots from the boot point, finds itself in the provisional entry, loads the role, and acks.
7. **Reconcile.** The new owner confirms each open item against the real system before acting on it. A human re-confirms each frozen item to the new owner, which lifts the freeze from step 2.
8. **Record.** Finalize the registry entry with the new owner, the date and the reason.

If the mailbox is down, the human re-seats by hand and steps 6 to 8 replay on the bus when it returns.

## Moving a job to a new agent

A handoff is an ordinary CATBus `ask` whose payload is a handoff packet *(proposed shape)*:

```json
{
  "v": "2",
  "event": "ask",
  "correlation_id": "550e8400-e29b-41d4-a716-446655440010",
  "sender": "orchestrator",
  "target": "spare-worker",
  "ttl_seconds": 86400,
  "payload": {
    "task": "handoff",
    "role": "worker-node",
    "why": "take-over",
    "state_card": "roles/worker-node/state.md",
    "open_threads": ["550e8400-e29b-41d4-a716-446655440003"],
    "expected_back": "ack, then a task with status echoing this correlation_id",
    "gates": ["outbound mail needs human GO"],
    "data_class": "internal"
  }
}
```

- `target` is the new owner's callsign. Here `spare-worker` takes over the `worker-node` role.
- `task: handoff` uses the existing `task` key that every `ask` already carries. `ttl_seconds` is the deadline for the follow-up.
- `why` is one of `take-over`, `consult` or `review`. Only `take-over` changes ownership in the registry, and it is sent by the `orchestrator`, which owns seating. A `dispatcher` can send `consult` or `review`.
- **Allowlist warning:** apart from `task`, these payload keys (and `data_class` values such as `internal`) are a *proposed* addition to the `ask` allowlist in [`secure-payload.md`](secure-payload.md). Fleets that strip unknown keys must allowlist them first, or the handoff silently arrives as an empty ask.
- A handoff never targets a multicast alias. It needs exactly one new owner.

The receiver should be able to answer "why am I getting this, and what do I owe back?" from the packet alone. The context itself stays in the record store and is linked, not pasted, so it isn't copied around and doesn't go stale.

## Broadcasting and making the most of every token

A multi-agent fleet isn't only about resilience. It's also how you get the most from every agent's skills and every token budget you already have.

- **Broadcast once, read many.** Fleet-wide guidance, status or a change of plan goes out as one `telemetry` or `task` message to a documented multicast alias (the envelope's `target` allows one, defined in your private registry), not as a separate ask to each agent. No ack is owed. Each alias is scoped to the roles that need the update. When priorities change, the roles that depend on it hear about it from the bus, and nobody has to remember who to tell.
- **Route by skill and budget.** Send each job to the agent best suited for it, and among those, prefer the one with budget left. Cheap or local models take routine work. Strong models are kept for the work that needs them.
- **Running out of budget is an outage.** When an agent hits its limit, its job moves to one that hasn't, using the same handoff as above. The role stays put and only the owner changes.
- **No silent stalls.** Agents report their budget state in heartbeats ([`architecture.md`](architecture.md) lists `telemetry` / `heartbeat`), as a number under `metrics` *(proposed key)*. Big jobs pause and say so, instead of dying mid-task.
- **Sparse by default.** Send finished packets, not chatty loops. Every wake and every reread of a big file costs tokens. Poll on a sensible schedule and cap how often any one thread can wake an agent.

Done well, a fleet of free and low-cost tiers, each doing what it does best and handing off when it's tapped out, can feel much bigger than any single seat.

**Guardrail:** every seat stays within its vendor's terms. Use one real account per person per service, and never rotate accounts to stretch free quotas. Pooling is about using what you legitimately have, not working around limits.

## Non-goals

- Automatic failover with no human in the loop for gated roles.
- A database or message broker requirement. A mailbox and a repository are enough to start.
- Copying every agent's full memory into every other agent, or shipping transcripts as context.
- Publishing live fleet keys, callsigns, seating or ops detail.

## Open questions

- The exact state-card schema and the size ceiling for it.
- How long bus threads must be kept to support recovery.
- Whether the handoff keys should join the `ask` allowlist, and which `data_class` values to standardize.
- How a fleet should score "remaining budget" across vendors that report it differently.
