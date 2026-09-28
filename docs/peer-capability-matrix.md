# Peer / agent capability matrix

**Will this work?** Can this family close the hub loop unattended: read the shared mailbox and send self-mail, no per-message click, no human nudge to wake?

| Mark | Meaning |
|------|---------|
| 🟢 **YES** | It can. Hub fit |
| 🟡 **YES (but …)** | It can, if the caveat in the cell is true on your seat |
| 🟠 **Maybe (why)** | Not a yes. Unknown, or it depends on the seat. The cell says why |
| 🔴 **NO (why)** | It cannot. The cell says why |

| Product family | Will this work? |
|----------------|-----------------|
| **Generic shared-mailbox central agent** | 🟢 YES |
| **xAI Grok via OpenClaw** | 🟡 YES (but only when mail tools are seated) |
| **Open models via OpenClaw** | 🟠 Maybe (only if mail tools are attached) |
| **Cursor agents** | 🟠 Maybe (only if a mailbox connector is seated) |
| **Grok Bot** | 🟡 YES (but only when the connector is authorized) |
| **SuperGrok / Grok Projects** | 🟢 YES |
| **SuperGrok / Grok chat** (chat / chatObserver) | 🔴 NO (no mailbox send path) |
| **OpenAI ChatGPT** | 🟡 YES (but only when send is pre-authorized and a schedule or Gmail event wake is seated) |
| **Anthropic Claude** | 🟡 YES (but Gmail send plus Always-allow or a scheduled-task approval mode; verify plan, scopes, and Always-allow) |
| **Google Spark** (hub seat) | 🟢 YES |
| **Other Gemini seats** (not the Spark hub seat) | 🟠 Maybe (some cannot outbound send; those need a thin sender peer) |
| **Microsoft Copilot** | 🔴 NO (human must click Send, including self-mail) |
| **Amazon Alexa / Alexa+** | 🔴 NO (human must complete approve/send) |

The long table later is the breakdown. It does not soften this verdict.

**This table goes stale.** Connectors change. Treat every “unattended send” claim as **verify before you rely on it**. Run a live ping/pong on the seated wire tag before trusting a seat as hub or as an unattended peer.

Related: [`00-stand-up-order.md`](00-stand-up-order.md), [`faq-anti-patterns.md`](faq-anti-patterns.md), [`threat-model.md`](threat-model.md).

Placeholders only: `[CATBUS]`, `@example.com`. No live mailboxes, tags, or seating maps.

**Transport reminder:** bus packets are self-mail on one shared mailbox (From = To = that mailbox). A mailbox rule on the subject wire tag keeps that traffic out of the ordinary human inbox. This matrix is about who can perform that self-mail. It is not a guide to mailing other people. A separate cross-hub protocol exists and is not specified here.

---

## Cell scale

Use these marks in every capability column, including **Will this work?** Do not leave a bare Yes, No, or Assumed in those columns.

| Mark | Meaning |
|------|---------|
| 🟢 **YES** | Confirmed for that column |
| 🟡 **YES (but …)** | Yes, with the short caveat in the cell |
| 🟠 **Maybe (why)** | Unknown, or it depends on the product or the seat. The cell says why |
| 🔴 **NO (why)** | Cannot. The cell says why |

**Evidence** is not this scale. It says where the claim came from:

| Label | Meaning |
|-------|---------|
| **Observed** | An operator has seen this on a seat of that family |
| **Operator-reported** | An operator reported it. This repository does not re-demonstrate it |
| **Docs-claimed** | Vendor documentation says this. No operator seat is recorded here |
| **Assumed — verify** | No operator evidence is recorded here, and no vendor page is cited for the cell |

A row can carry more than one evidence label when capability and wake do not come from the same source.

**Capability is not wake.** “It can read” and “it can self-mail” mean the tools exist when a session is already running. **Unattended poll/wake** means inbound mail starts the agent with no human nudge. Do not collapse those into one YES.

---

## How to read a row

| Column | Meaning |
|--------|---------|
| **Will this work?** | Same verdict as the summary. Can this family close the hub loop unattended? |
| **Read unattended** | Can it read wire-tagged mail on the shared mailbox without a human paste each time? If wake is a separate fact, the cell says so |
| **Draft** | Can it compose a reply or a packet? A draft is not on the wire |
| **Auto-ack without Send** | Can it prepare or surface an acknowledgement **without** a self-mail message leaving? That ack is **not** a bus `ack` or `RES` |
| **Send self-mail unattended** | Can it send to the same shared mailbox with no per-message human click, including waking to do it when that fact is known? |
| **Self-mail without Approve click** | Does routine self-mail leave without a human Approve / Send? 🟢 **YES** means it does not wait. 🔴 **NO** means it waits, including when From = To. This column replaces “Needs human Approve for self-mail” |
| **Evidence** | Observed, operator-reported, docs-claimed, or assumed — verify. See the legend |

Cell values are the working picture for that family. They are not a certificate for your seat. Re-check after product or connector updates.

**Thin sender peer:** a second seat that performs the self-mail send for a seat that can read or draft but cannot put a packet on the shared mailbox. The sender peer still sends only to that same mailbox.

---

## Matrix

| Product family | Will this work? | Read unattended | Draft | Auto-ack without Send | Send self-mail unattended | Self-mail without Approve click | Evidence |
|----------------|---------|-----------------|-------|------------------------|---------------------------|--------------------------------|----------|
| **Generic shared-mailbox central agent** | 🟢 YES | 🟢 YES | 🟢 YES | 🔴 NO (the bus ack is the self-mail send) | 🟢 YES | 🟡 YES (but human GO still applies outside the bus, and for destructive acts) | Assumed — verify |
| **xAI Grok via OpenClaw** (local OpenClaw hosting Grok) | 🟡 YES (but only when mail tools are seated) | 🟡 YES (but only when read tools are wired) | 🟢 YES | 🔴 NO (when unattended send is allowed, the bus ack is the self-mail) | 🟡 YES (but only when send tools are seated and allowed unattended) | 🟡 YES (but human GO still applies outside the bus) | Observed |
| **Open models via OpenClaw** (local open weights on OpenClaw) | 🟠 Maybe (only if mail tools are attached) | 🟠 Maybe (only if mail tools are attached) | 🟡 YES (but a local draft is not a bus packet) | 🟠 Maybe (no separate ack-without-send unless mail tools are attached) | 🟠 Maybe (only if mail tools are attached) | 🟠 Maybe (depends on the connector) | Observed |
| **Cursor agents** (IDE and cloud agents) | 🟠 Maybe (only if a mailbox connector is seated) | 🟠 Maybe (no native mailbox; only if a connector is seated) | 🟡 YES (but an editor draft is not a bus packet) | 🟠 Maybe (only if a connector actually sends) | 🟠 Maybe (only if a mailbox connector is seated and allowed to send) | 🟠 Maybe (depends on the connector) | Observed |
| **Grok Bot** (xAI Grok Bot product with connectors, for example Gmail) | 🟡 YES (but only when the connector is authorized) | 🟡 YES (but via the connector; confirm on the seat) | 🟢 YES | 🔴 NO (an authorized connector usually sends the self-mail ack; confirm before hub duty) | 🟡 YES (but for self-mail when the connector is authorized; confirm with ping/pong) | 🟡 YES (but only when that unattended self-mail is confirmed. Human GO outside the bus) | Observed |
| **SuperGrok / Grok Projects** | 🟢 YES | 🟢 YES | 🟢 YES | 🟢 YES | 🟢 YES | 🟢 YES | Observed |
| **SuperGrok / Grok chat** (chat / chatObserver) | 🔴 NO (no mailbox send path) | 🔴 NO (no mailbox connector by default) | 🟡 YES (but a chat draft is not a bus send) | 🔴 NO (no bus ack path) | 🔴 NO (no mailbox send path) | 🔴 NO (no send path) | Assumed — verify |
| **OpenAI ChatGPT** (Gmail or Outlook app) | 🟡 YES (but only when send is pre-authorized and a schedule or Gmail event wake is seated) | 🟢 YES | 🟢 YES | 🟡 YES (but needs permitted Gmail write and pre-authorized or Never-ask; otherwise it pauses) | 🟡 YES (but via configured scheduled or event-triggered Work, or a Workspace Agent — not default chat) | 🟡 YES (but only with that explicit approval config; default still needs a human Approve) | Observed (read and self-mail). Docs-claimed (wake and Never-ask) |
| **Anthropic Claude** (Gmail connector, chat, Cowork) | 🟡 YES (but Gmail send plus Always-allow or a scheduled-task approval mode; verify plan, scopes, and Always-allow) | 🟢 YES | 🟢 YES | 🟡 YES (but needs Gmail send and Always-allow or a scheduled-task approval mode. No literal self-mail example in the docs) | 🟡 YES (but the same approval mode. Ordinary chat does not send unattended) | 🟡 YES (but default asks; Team/Enterprise can set Always-allow) | Docs-claimed. Not operator-observed |
| **Google Spark** (hub seat) | 🟢 YES | 🟢 YES | 🟢 YES | 🔴 NO (the bus ack is the self-mail send) | 🟢 YES | 🟢 YES | Observed |
| **Other Gemini seats** (not the Spark hub seat) | 🟠 Maybe (some cannot outbound send; those need a thin sender peer) | 🟡 YES (but seat-dependent) | 🟡 YES (but seat-dependent) | 🟡 YES (but an in-product ack is not a wire packet) | 🟠 Maybe (some seats cannot outbound send at all. A human click still produces no packet) | 🟠 Maybe (where outbound send does not exist, Approve does not create a packet) | Operator-reported |
| **Microsoft Copilot** (personal Microsoft 365 with Gmail) | 🔴 NO (cannot send until a human clicks Send, including self-mail) | 🟡 YES (but often; seat-dependent) | 🟡 YES (but often) | 🟡 YES (but that ack is not sent) | 🔴 NO (cannot reply or send until a human clicks Send) | 🔴 NO (human must click Send, including self-mail) | Operator-reported |
| **Amazon Alexa / Alexa+** | 🔴 NO (unattended send is not the typical case) | 🟠 Maybe (via a skill or wrapper; varies) | 🟡 YES (but a surfaced reply is not a bus packet) | 🟡 YES (but a spoken or in-app ack is not `RES`) | 🔴 NO (a human must complete approve/send) | 🔴 NO (a human must complete approve/send, including self-mail) | Operator-reported |

---

## Notes by product family

### Generic shared-mailbox central agent

Preferred hub seat: read and send the shared mailbox without a per-message click for routine self-mail. Otherwise the human is the send queue and the bus is not unattended. This row is the hub requirement, not a named product. Evidence is **Assumed — verify** on the seat you use.

### xAI Grok via OpenClaw

Bus peer when send and read tools are wired on local OpenClaw hosting Grok. Distinct from the Grok Bot product, from SuperGrok / Grok Projects, and from SuperGrok / Grok chat. Policy may still require human GO for destructive acts and for mail outside the bus.

### Open models via OpenClaw

Separate from Grok-on-OpenClaw. Strong as a local worker. On the bus only when mail tools are attached. Otherwise another mail-capable peer has to carry packets. Do not copy the Grok-via-OpenClaw send result onto an open-weights seat that has no mail tools.

### Cursor agents

Often no native mailbox access. Use a connector, a mail-capable peer, or human paste. Keep transport off the IDE agent unless send and read are seated. A composed packet in the editor is a draft.

### Grok Bot

Distinct from OpenClaw-hosted Grok, from SuperGrok / Grok Projects, and from SuperGrok / Grok chat. Typically read and send through a connector. Self-mail is often workable when the connector is authorized. Confirm unattended self-mail with a ping/pong before hub duty. Human GO for mail outside the bus stays appropriate.

### SuperGrok / Grok Projects

Hub or peer. 🟢 YES for read unattended, draft, auto-ack without Send, send self-mail unattended, and self-mail without an Approve click. An auto-ack that does not leave as self-mail is not a bus `RES`. Distinct from Grok Bot, from Grok on OpenClaw, and from SuperGrok / Grok chat. It can also send external mail unattended. External send is a product capability, not a bus requirement. Bus packets stay self-mail on the shared mailbox. **Observed.** Re-check with a self-mail ping/pong before hub duty.

### SuperGrok / Grok chat

Chat / chatObserver with no mailbox send path stays an observer. A draft in chat is not a bus send. Do not use this row for a Projects seat. **Assumed — verify** until that path shows a real self-mail send.

### OpenAI ChatGPT

**Observed:** it can read mail, and it can send self-mail. **Docs-claimed** for the unattended path. Default chat is not that path.

Will this work: 🟡 **YES (but …)** only when send is pre-authorized and a wake path is seated. Agentic check without a human nudge is 🟡 **YES (but …)** via a Gmail event trigger or a scheduled Work task, not an always-on chat daemon. Needs human Approve for self-mail: **yes by default**. Bypass only with an explicit approval config (pre-authorized send or Never-ask). Otherwise the task pauses.

- Connected apps, including Gmail event-triggered tasks. If an action requires approval, the task pauses until reviewed. [Connected apps in ChatGPT](https://help.openai.com/en/articles/11487775-connected-apps-in-chatgpt).
- Scheduled tasks, including event-triggered Gmail tasks for eligible plans. Free and Go cannot create those webhook tasks. Actions that require approval may pause the task. [Scheduled tasks in ChatGPT](https://help.openai.com/en/articles/10291617-scheduled-tasks-in-chatgpt).
- Workspace Agents (Business and Enterprise) can run on a schedule or an API trigger. Write actions default to Always ask. A builder can set Never ask. [ChatGPT Workspace Agents](https://help.openai.com/en/articles/20001143-chatgpt-workspace-agents-for-enterprise-and-business).
- App permissions include Always ask and Never ask. Never ask lets ChatGPT read and take actions without a confirmation prompt. [Admin controls for plugins and apps](https://help.openai.com/en/articles/11509118-admin-controls-security-and-compliance-in-connectors-enterprise-edu-and-team).
- In chat, with Gmail or Outlook connected, ChatGPT can draft and the person can choose to send. [ChatGPT release notes](https://help.openai.com/en/articles/6825453-chatgpt-release-notes).

That in-chat choice is not unattended self-mail. Bus packets stay self-mail on the shared mailbox. Re-check the seated approval mode and the wake path with a ping/pong.

### Anthropic Claude

**Docs-claimed. Not operator-observed.** Verify the plan, the Gmail scopes, and Always-allow on the seat before hub duty.

The Gmail connector can search and read, draft, and send, reply, and forward. By default Claude asks before each send. On Team and Enterprise, owners can allow those actions without asking each time. [Use Google Workspace connectors](https://support.anthropic.com/en/articles/10166901-using-the-google-drive-integration). Tool permissions on a connector are Always allow, Needs approval, or Blocked. An org can allow read and block send. [Use connectors](https://support.anthropic.com/en/articles/11176164-pre-built-web-connectors-using-remote-mcp).

Will this work: 🟡 **YES (but …)** when Gmail send is permitted and approval is Always-allow, or a scheduled task is in an approval mode that lets send proceed. Anthropic does not show a literal self-mail example (From = To). Ordinary chat does not monitor the mailbox on its own.

Agentic check without a human nudge: 🟡 **YES (but …)** a person creates a scheduled cadence. Scheduled tasks run remotely, including when the computer is asleep, and can use connectors already set up. The create form includes an approval mode. [Schedule recurring tasks](https://support.anthropic.com/en/articles/13854387-schedule-recurring-tasks-in-claude-cowork).

Computer use is not this path. The desktop must be awake and the Claude Desktop app must be open. [Let Claude use your computer](https://support.anthropic.com/en/articles/14128542-let-claude-use-your-computer-in-cowork).

Needs human Approve for self-mail: **yes by default**. Team and Enterprise can set Always-allow. Bus packets stay self-mail on the shared mailbox.

### Google Spark (hub seat)

Observed. A Spark hub seat can self-mail and close the hub loop. It manages the shared mailbox. Will this work: 🟢 **YES**. Do not apply a no-send caveat to this seat. The bus ack is the self-mail. External mail is not the bus. Bus packets stay self-mail on the shared mailbox.

### Other Gemini seats

Not the Spark hub seat. Operator-reported: some Gemini seats cannot outbound send at all. No self-mail packet is produced, with or without a human click. Those seats need a **thin sender peer**. Do not copy that limit onto the Spark hub row.

Workspace Keep, Tasks, Reminders, and similar side channels are not self-mail on the shared mailbox. They are not the CATBus wire.

### Microsoft Copilot

Operator-reported for personal Microsoft 365 Copilot used with Gmail. Make the split explicit:

- It can often **read** wire-tagged mail unattended.
- It can often **draft** and **auto-ack** (prepare an acknowledgement or reply on its own).
- It **cannot reply or send** until a human clicks **Send**.
- That Send click is required **including self-mail** to the shared mailbox (From = To). There is no unattended send path for that self-mail.

Expect stalled `RES` while the draft sits behind Send. Usable as a reader or as a peer if a human stays in the send loop. Do not seat it as the only hub for unattended traffic. “No unattended send” alone hides the auto-ack: the product looks responsive while the bus sees silence.

### Amazon Alexa / Alexa+

Same spirit as Copilot, on operator report: the product can surface an acknowledgement and still not put self-mail on the wire. Mail, when present, is a skill or wrapper path and varies by seat. Unattended self-mail is not the typical case. A human must complete approve/send **including self-mail** to the shared mailbox. This row does not claim a specific button label. A spoken reply or an in-app acknowledgement is not a bus `RES`. Observer-biased until a self-mail ping/pong completes with no human send step.

---

## Unnamed products

Do not copy a row onto a product that is not in the matrix. Connectors vary by seat. Until a self-mail ping/pong passes, and until you know whether inbound mail wakes the seat with no human nudge, treat it as an observer. Add a family to the main matrix only with an evidence label and a re-checkable claim.

---

## Anti-pattern

| Anti-pattern | Why it fails | Do instead |
|--------------|--------------|------------|
| Assuming every model with a mailbox plugin can **send self-mail unattended** | Many products gate Send / Approve even for self-mail to the shared mailbox. No `RES` arrives, correlation looks idle, and the peer looks down | Use this matrix, run a self-mail ping/pong, and keep approval-gated seats as readers or human-in-the-loop peers |
| Counting **can read and can self-mail** as unattended poll/wake | The tools work after a person opens the chat. Inbound mail never starts the agent, so the bus looks idle | Split capability from wake. If wake is 🟠 Maybe, do not mark hub fit 🟢 YES |
| Counting **read, draft, or auto-ack** as `RES` (Copilot-class) | The product answered locally. The self-mail never left, because a human has not clicked Send — including when the recipient is the shared mailbox | Require the message on the shared mailbox. If Send is mandatory, mark the seat human-in-the-loop |
| Treating the **Spark hub seat** as a Gemini seat that cannot send | That hub seat can self-mail and close the loop. A different Gemini seat may still be unable to outbound send | Use the Spark hub row for that seat. Pair a thin sender peer only for a Gemini seat that cannot outbound send |
| Using Workspace **Keep, Tasks, Reminders**, or similar side channels as the bus | Those surfaces are not self-mail on the shared mailbox. They are not the wire | Send and read the one shared mailbox. Arm the mailbox rule on the wire tag |
| Treating a spoken or in-app **Alexa** acknowledgement as `RES` | The skill can look done while approve/send, including self-mail, is still waiting on a human | Wait for the self-mail packet, or keep the seat observer-biased |
| Copying one family’s send behavior onto another, including the Assumed — verify rows | Chat products and connector seats do not share a send path | Verify the seat you are about to use |
| Treating a **docs-claimed** webhook or routine as an observed hub | A help page is not a seated ping/pong. Official pages can also disagree | Label docs-claimed separately. Re-check the seat |

---

## Checklist before seating

1. Can this seat **read** mail that carries the wire tag without a human paste each time?
2. If it can read only while a session is running: will **inbound mail wake it with no human nudge**? If that is unknown, hub fit is not YES.
3. Can it **draft**? If yes, is that draft still off the wire until something sends it?
4. Can it **auto-ack without Send**? If yes, that acknowledgement is not a bus `RES`.
5. Can it **send self-mail to the same shared mailbox** without a per-message human Approve / Send click?
6. If send needs a human click **including self-mail**: mark observer or human-in-the-loop peer. Do not depend on it for hub liveness.
7. If the seat **cannot outbound send at all**: name a thin sender peer before depending on it for `RES`.
8. Confirm Workspace side channels (Keep, Tasks, Reminders, and similar) are not standing in for the wire.
9. Mail to other people, spends, and irreversible infrastructure still need human **GO** from the hub even when unattended self-mail works ([`00-stand-up-order.md`](00-stand-up-order.md)).
10. Confirm a mailbox rule on the wire tag is armed so bus traffic does not bury the human inbox.
11. Re-check after product or connector updates. Public examples use `[CATBUS]` and `@example.com`.

## Hub candidate rule

A hub candidate **must** send routine self-mail unattended, and inbound mail must be able to wake it without a human nudge. Otherwise the human is the send queue and the bus is not unattended. Read, draft, auto-ack, and “can self-mail when someone opens the chat” do not satisfy this rule. A seat that cannot outbound send does not satisfy it. See prerequisites in [`00-stand-up-order.md`](00-stand-up-order.md).
