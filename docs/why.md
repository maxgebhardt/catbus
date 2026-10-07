# Why CATBus exists

Anyone can hand over an ask. What nobody hands over well is the context behind it.

Think about the last time you inherited a project. Months of history arrived in a few meetings, a shared folder, and "ping me if you have questions." People aren't built to transfer a full sense of state, so we lean on walkthroughs and hope it sticks.

AI agents have the same problem, only worse: **they wake up with amnesia.** Every new session, new seat, or new tool starts from zero unless something hands the context back.

## The problem: state lives in whoever holds the role

Agents run out of tokens. Sessions end. Models get swapped. People go on leave, get pulled onto a fire drill, or move on. The job still needs doing.

When a role lives inside the agent or the person doing it, every move is a rebuild by hand. The fix is to keep the role somewhere else: in one place, documented or delegated, so when it moves it doesn't start over. **It just gets a new owner.**

## Hand off agent to agent

As people adopt AI, you adopt an AI partner: something you work with as a close counterpart to block and tackle. But as I always say, **bot for bot, human for human.**

When the bot works with you, it learns a tremendous amount of context, but what visibility do you have into that context? And it also holds the history of a project or account better than you do. So why, when the AI deals with so much of the work and nuance, do we hand off at the meatsack layer?

Agent-to-agent transfer is the future. Bringing someone into your project? Here's a CATBus-standard handoff, and the agent that knows you will get you up to speed and work it into your workflow for you. No stupid meetings, no wasted breath. The humans can connect up after the handoff to talk strategy, not operational and tactical block and tackle.

## What we're building toward

- **Recover state.** An agent that wakes up cold reads one boot point, learns who it is, what role it holds, and what to load, and picks up where the last owner stopped.
- **Transfer state.** When work changes hands, the outgoing side packages where it's been, where it's going, what's open, and why the receiver is being brought in: to take over, to consult, or to review. The receiver's own assistant walks them through it on their time.
- **Measure it honestly.** A handoff is good when the receiver has little left to rebuild. That's the yardstick, not how much text was passed along.

## The principles

1. **Messages carry handoffs; a repository holds memory.** If either goes down, the other helps everyone remember what was going on.
2. **Governed context, not transcripts.** Hand over the state that matters, in a standard shape, not a dump of everything that was said.
3. **Memory you can read, edit, and ratify beats memory that's done to you.** Shared memory is created where its owner can see it and approve it.
4. **No hub in the infrastructure, a hub in coordination.** There's no broker or server. There is a coordination role (protocol owner, role registry, boot point), and it can be re-seated like any other role.
5. **Governance before intelligence.** Shared context means shared exposure. It needs a standard format, a clear source of truth, need-to-know access, an audit trail, a human in charge of anything sensitive, and a way to correct or withdraw what went out.

## Where it stands

Today CATBus is the messaging layer: asynchronous agent-to-agent tasking over ordinary email, with no extra infrastructure. Recovering and transferring state is the design work happening next, in the open, in [`fleet-continuity.md`](fleet-continuity.md).
