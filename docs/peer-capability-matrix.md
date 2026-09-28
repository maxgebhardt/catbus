# Peer / agent capability matrix

**Will this work?** means the family can close the hub loop unattended: read the shared mailbox and send self-mail, with no per-message click and no human nudge.

## Legend

| Mark | Meaning |
|------|---------|
| 🟢 **YES** | Confirmed |
| 🟡 **YES (but …)** | Yes, if the caveat in the cell is true on your seat |
| 🟠 **Maybe (why)** | Not a yes. The cell says why |
| 🔴 **NO (why)** | Cannot. The cell says why |

| Evidence | Meaning |
|----------|---------|
| **Observed** | An operator has seen it on a seat of that family |
| **Operator-reported** | An operator reported it. This repo does not re-demonstrate it |
| **Docs-claimed** | Vendor docs say this. No operator seat is recorded here |
| **Assumed — verify** | No operator evidence, and no vendor page for the cell |

**Inbound-mail wake** is not the hub verdict. Three paths, not interchangeable:

- webhook / push / Gmail-event trigger
- scheduled poll only
- human opens chat

A schedule is not a webhook. Opening the chat is not a wake.

---

## Will this work?

| Product family | Will this work? | Inbound-mail wake |
|----------------|-----------------|-------------------|
| **Generic shared-mailbox central agent** | 🟢 YES | 🟢 YES |
| **xAI Grok via OpenClaw** | 🟡 YES (but mail tools seated) | 🟡 YES (but mail tools seated; webhook not claimed) |
| **Open models via OpenClaw** | 🟠 Maybe (only with mail tools) | 🟠 Maybe (only with mail tools) |
| **Cursor agents** | 🟠 Maybe (only with a mail connector) | 🔴 NO (human opens the editor) |
| **Grok Bot** | 🟡 YES (but connector authorized) | 🟡 YES (but connector authorized; webhook not claimed) |
| **SuperGrok / Grok Projects** | 🟢 YES | 🟢 YES (Observed; no webhook API claimed) |
| **SuperGrok / Grok chat** | 🔴 NO (no send path) | 🔴 NO (human opens chat) |
| **OpenAI ChatGPT** | 🟡 YES (but pre-authorized send + seated wake) | 🟡 YES (but Gmail-event; plan/config) |
| **Anthropic Claude** | 🟡 YES (but Always-allow or schedule mode) | 🟡 YES (but schedule only) |
| **Google Spark** (hub seat) | 🟢 YES | 🟡 YES (but Observed mail handling; no HTTP webhook) |
| **Other Gemini seats** | 🟠 Maybe (some cannot send) | 🟠 Maybe |
| **Microsoft Copilot** | 🔴 NO (human must click Send) | 🔴 NO (human must click Send) |
| **Amazon Alexa / Alexa+** | 🔴 NO (human must approve/send) | 🔴 NO (human must approve/send) |

The detail table does not change these verdicts. Re-check with a self-mail ping/pong. Public examples use `[CATBUS]` and `@example.com`.

Bus packets are self-mail on one shared mailbox (From = To). This page is not a guide to mailing other people.

---

## Detail

| Column | Question |
|--------|----------|
| **Will this work?** | Same verdict as the table above |
| **Inbound-mail wake** | Gmail-event / webhook, schedule only, or human opens chat |
| **Read unattended** | Read wire-tagged mail without a human paste |
| **Draft** | Compose a packet. A draft is not on the wire |
| **Auto-ack without Send** | Surface an ack that does not leave as mail. That is not `RES` |
| **Send self-mail unattended** | Send to the same mailbox with no per-message click |
| **Self-mail without Approve click** | 🟢 YES means routine self-mail does not wait. 🔴 NO means it does, including From = To |
| **Evidence** | Observed, operator-reported, docs-claimed, or assumed — verify |

**Thin sender peer:** a second seat that sends self-mail for a seat that cannot.

| Product family | Will this work? | Inbound-mail wake | Read | Draft | Auto-ack without Send | Send self-mail unattended | No Approve click | Evidence |
|----------------|-----------------|-------------------|------|-------|------------------------|---------------------------|------------------|----------|
| **Generic hub** | 🟢 YES | 🟢 YES | 🟢 YES | 🟢 YES | 🔴 NO (the ack is the send) | 🟢 YES | 🟡 YES (but human GO still applies outside the bus) | Assumed — verify |
| **Grok via OpenClaw** | 🟡 YES (but mail tools seated) | 🟡 YES (but mail tools; webhook not claimed) | 🟡 YES (but read tools wired) | 🟢 YES | 🔴 NO (the ack is the send) | 🟡 YES (but send tools seated) | 🟡 YES (but human GO outside the bus) | Observed |
| **Open models via OpenClaw** | 🟠 Maybe (only with mail tools) | 🟠 Maybe (only with mail tools) | 🟠 Maybe (only with mail tools) | 🟡 YES (but local draft is not a packet) | 🟠 Maybe (only with mail tools) | 🟠 Maybe (only with mail tools) | 🟠 Maybe (depends on the connector) | Observed |
| **Cursor agents** | 🟠 Maybe (only with a mail connector) | 🔴 NO (human opens the editor) | 🟠 Maybe (only with a connector) | 🟡 YES (but editor text is not a packet) | 🟠 Maybe (only if the connector sends) | 🟠 Maybe (only if the connector may send) | 🟠 Maybe (depends on the connector) | Observed |
| **Grok Bot** | 🟡 YES (but connector authorized) | 🟡 YES (but connector; webhook not claimed) | 🟡 YES (but via the connector) | 🟢 YES | 🔴 NO (the connector usually sends the ack) | 🟡 YES (but confirm with ping/pong) | 🟡 YES (but only once that send is confirmed) | Observed |
| **SuperGrok / Grok Projects** | 🟢 YES | 🟢 YES (Observed; no webhook API claimed) | 🟢 YES | 🟢 YES | 🟢 YES | 🟢 YES | 🟢 YES | Observed |
| **SuperGrok / Grok chat** | 🔴 NO (no send path) | 🔴 NO (human opens chat) | 🔴 NO (no mailbox connector) | 🟡 YES (but chat text is not a send) | 🔴 NO (no ack path) | 🔴 NO (no send path) | 🔴 NO (no send path) | Assumed — verify |
| **OpenAI ChatGPT** | 🟡 YES (but pre-authorized send + seated wake) | 🟡 YES (but Gmail-event; plan/config) | 🟢 YES | 🟢 YES | 🟡 YES (but Never-ask or pre-authorized write; else it pauses) | 🟡 YES (but Work event/schedule or a Workspace Agent; not default chat) | 🟡 YES (but only with that config; default still asks) | Observed (read, self-mail). Docs-claimed (wake) |
| **Anthropic Claude** | 🟡 YES (but Always-allow or schedule mode) | 🟡 YES (but schedule only) | 🟢 YES | 🟢 YES | 🟡 YES (but Always-allow or schedule mode; no self-mail example) | 🟡 YES (but same mode; ordinary chat does not send alone) | 🟡 YES (but default asks; Team/Enterprise can Always-allow) | Docs-claimed |
| **Google Spark** (hub seat) | 🟢 YES | 🟡 YES (but Observed mail handling; no HTTP webhook) | 🟢 YES | 🟢 YES | 🔴 NO (the ack is the send) | 🟢 YES | 🟢 YES | Observed |
| **Other Gemini seats** | 🟠 Maybe (some cannot send) | 🟠 Maybe | 🟡 YES (but seat-dependent) | 🟡 YES (but seat-dependent) | 🟡 YES (but an in-product ack is not a packet) | 🟠 Maybe (some cannot outbound send at all) | 🟠 Maybe (no packet if send does not exist) | Operator-reported |
| **Microsoft Copilot** | 🔴 NO (human must click Send) | 🔴 NO (human must click Send) | 🟡 YES (but often) | 🟡 YES (but often) | 🟡 YES (but the ack is not sent) | 🔴 NO (human must click Send) | 🔴 NO (human must click Send) | Operator-reported |
| **Amazon Alexa / Alexa+** | 🔴 NO (human must approve/send) | 🔴 NO (human must approve/send) | 🟠 Maybe (skill or wrapper) | 🟡 YES (but a spoken reply is not a packet) | 🟡 YES (but a spoken ack is not `RES`) | 🔴 NO (human must approve/send) | 🔴 NO (human must approve/send) | Operator-reported |

---

## Notes

### Generic hub

The hub requirement, not a named product. Read and send routine self-mail with no per-message click. **Assumed — verify** on the seat you use.

### xAI Grok via OpenClaw

Bus peer when mail tools are seated. Distinct from Grok Bot, SuperGrok / Grok Projects, and SuperGrok / Grok chat. Human GO still applies outside the bus. Webhook vs poll is not claimed.

### Open models via OpenClaw

Not the Grok-on-OpenClaw result. On the bus only when mail tools are attached.

### Cursor agents

No native mailbox. A connector, a mail-capable peer, or a human paste. Editor text is a draft.

### Grok Bot

Read and send through a connector when it is authorized. Confirm unattended self-mail with a ping/pong. Human GO outside the bus. Webhook vs poll is not claimed.

### SuperGrok / Grok Projects

🟢 YES on the full lane: read, draft, auto-ack without Send, unattended self-mail, no Approve click. Inbound wake is 🟢 YES, **Observed**: inbound mail is processed with no human nudge. No webhook API is claimed. External mail unattended is a product capability, not a bus requirement. Bus packets stay self-mail. An unsent ack is not `RES`.

### SuperGrok / Grok chat

No mailbox send path. Wake is a human opening chat. Do not use this row for a Projects seat. **Assumed — verify.**

### OpenAI ChatGPT

**Observed:** read and self-mail. **Docs-claimed:** the unattended path. Default chat is a human nudge.

Will this work only when send is pre-authorized and a wake is seated. Inbound wake is a Gmail-event trigger on eligible Work when that task is configured. A schedule is a different path. Free and Go cannot create those event tasks. If an action needs approval, the task pauses. Never-ask, or pre-authorized write, is the bypass. Workspace Agents (Business and Enterprise) can run on a schedule or an API trigger; write actions default to Always ask.

- [Connected apps](https://help.openai.com/en/articles/11487775-connected-apps-in-chatgpt)
- [Scheduled tasks](https://help.openai.com/en/articles/10291617-scheduled-tasks-in-chatgpt)
- [Workspace Agents](https://help.openai.com/en/articles/20001143-chatgpt-workspace-agents-for-enterprise-and-business)
- [Admin controls](https://help.openai.com/en/articles/11509118-admin-controls-security-and-compliance-in-connectors-enterprise-edu-and-team)
- [Release notes](https://help.openai.com/en/articles/6825453-chatgpt-release-notes)

### Anthropic Claude

**Docs-claimed. Not operator-observed.** Verify plan, Gmail scopes, and Always-allow.

The Gmail connector can read, draft, and send. Default is ask-before-send. Team and Enterprise can set Always allow, Needs approval, or Blocked. Will this work when send is allowed and a scheduled task’s approval mode lets it proceed. No literal self-mail example (From = To). Ordinary chat does not watch the inbox. Wake is a person-created schedule only, not a Gmail-event or webhook. Computer use is not this path: the desktop must be awake and the Desktop app open.

- [Google Workspace connectors](https://support.anthropic.com/en/articles/10166901-using-the-google-drive-integration)
- [Connectors](https://support.anthropic.com/en/articles/11176164-pre-built-web-connectors-using-remote-mcp)
- [Scheduled tasks](https://support.anthropic.com/en/articles/13854387-schedule-recurring-tasks-in-claude-cowork)
- [Computer use](https://support.anthropic.com/en/articles/14128542-let-claude-use-your-computer-in-cowork)

### Google Spark (hub seat)

**Observed.** 🟢 YES hub. It self-mails and manages the mailbox. Wake is 🟡 YES (but …): inbound mail is handled with no human nudge. No HTTP webhook is claimed. Do not apply a no-send caveat to this seat. The bus ack is the self-mail.

### Other Gemini seats

Not the Spark hub seat. **Operator-reported:** some cannot outbound send at all. Those need a thin sender peer. Keep, Tasks, and Reminders are not the wire.

### Microsoft Copilot

**Operator-reported.** Often reads, drafts, and auto-acks. Cannot send until a human clicks Send, including self-mail. The draft looks done. The bus sees silence.

### Amazon Alexa / Alexa+

**Operator-reported.** A spoken or in-app ack is not `RES`. A human must finish approve/send, including self-mail. No mail webhook is claimed.

---

## Do not

| Mistake | Do instead |
|---------|------------|
| Treating a mailbox plugin as unattended self-mail | Ping/pong. Approval-gated seats stay human-in-the-loop |
| Treating read or self-mail as a wake | Use the wake column. Human-open-chat is 🔴 NO |
| Treating a schedule as a Gmail-event wake | Schedule-only stays 🟡 YES (but …) |
| Treating read, draft, or auto-ack as `RES` | The self-mail has to leave |
| Treating the Spark hub seat as a no-send Gemini seat | Spark hub is 🟢 YES. A thin sender peer is for other Gemini seats that cannot send |
| Using Keep, Tasks, or Reminders as the bus | Self-mail on the one shared mailbox |
| Treating an Alexa utterance as `RES` | Wait for the self-mail |
| Copying one family’s send path onto another | Verify the seat |
| Treating a docs-claimed routine as Observed | Label it docs-claimed. Re-check the seat |

Unnamed products stay observers until a ping/pong passes and the wake path is known.

## Hub rule

A hub must send routine self-mail unattended, and inbound mail must wake it with no human nudge. Read, draft, auto-ack, and “can self-mail after someone opens the chat” do not qualify. A seat that cannot outbound send needs a thin sender peer. Human GO still applies outside the bus. See [`00-stand-up-order.md`](00-stand-up-order.md).
