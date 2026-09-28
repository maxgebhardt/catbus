# Peer / agent capability matrix

**Will this work?** means the family can close the hub loop unattended: read the shared mailbox and send self-mail, with no per-message click and no human nudge. Read or send only after a human asks is not this verdict.

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

A schedule is not a webhook. Opening the chat is not a wake. This column stays. Do not fold it back into **Will this work?**.

---

## Will this work?

Product names link to that vendor’s own site. [Google Spark](https://gemini.google.com/spark) is a beta feature. The generic hub row is the requirement, not a product, so it stays unlinked.

| Product family | Will this work? | Inbound-mail wake |
|----------------|-----------------|-------------------|
| **Generic shared-mailbox central agent** | 🟢 YES | 🟢 YES |
| [**xAI Grok via OpenClaw**](https://docs.openclaw.ai/providers/xai) | 🟡 YES (but mail tools seated) | 🟡 YES (but mail tools seated; webhook not claimed) |
| [**Open models via OpenClaw**](https://openclaw.ai) | 🟠 Maybe (only with mail tools) | 🟠 Maybe (only with mail tools) |
| [**Cursor agents**](https://cursor.com) | 🟡 YES (but mail connector + poll wake) | 🟡 YES (but poll only; not a Gmail webhook) |
| [**Grok Bot**](https://x.ai/bot) | 🟡 YES (but connector authorized) | 🟡 YES (but connector authorized; webhook not claimed) |
| [**SuperGrok / Grok Projects**](https://grok.com) | 🟢 YES | 🟢 YES (Observed; no webhook API claimed) |
| [**SuperGrok / Grok chat**](https://grok.com) | 🔴 NO (human must ask) | 🔴 NO (human opens chat) |
| [**ChatGPT (Custom GPT / Actions or Work)**](https://chatgpt.com) | 🟡 YES (but pre-authorized send + seated wake) | 🟡 YES (but Gmail-event or schedule; plan/config) |
| [**ChatGPT consumer web**](https://chatgpt.com) | 🔴 NO (consumer web chat) | 🔴 NO (human opens chat) |
| [**Claude (MCP / Desktop)**](https://claude.com/download) | 🟡 YES (but Always-allow or schedule mode) | 🟡 YES (but schedule only) |
| [**claude.ai web**](https://claude.ai) | 🔴 NO (web chat) | 🔴 NO (human opens the page) |
| [**Google Spark**](https://gemini.google.com/spark) (hub seat, beta) | 🟡 YES (but ephemeral turns; batch wake ~15–60m) | 🟡 YES (but batch ~15–60m or a human turn; operator-reported webhook, not a live socket) |
| [**Other Gemini seats**](https://gemini.google.com) | 🔴 NO (will not wake on inbound mail) | 🔴 NO (will not wake on inbound send) |
| [**Microsoft Copilot**](https://copilot.microsoft.com) (chat) | 🔴 NO (human must click Send) | 🔴 NO (human opens chat) |
| [**Copilot Tasks**](https://support.microsoft.com/en-us/microsoft-copilot/using-copilot-tasks) | 🟠 Maybe (schedule; sending mail still asks) | 🟡 YES (but schedule only; not inbound mail) |
| [**Amazon Alexa / Alexa+**](https://alexa.amazon.com) | 🔴 NO (human must approve/send) | 🔴 NO (human must approve/send) |

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
| [**Grok via OpenClaw**](https://docs.openclaw.ai/providers/xai) | 🟡 YES (but mail tools seated) | 🟡 YES (but mail tools; webhook not claimed) | 🟡 YES (but read tools wired) | 🟢 YES | 🔴 NO (the ack is the send) | 🟡 YES (but send tools seated) | 🟡 YES (but human GO outside the bus) | Observed |
| [**Open models via OpenClaw**](https://openclaw.ai) | 🟠 Maybe (only with mail tools) | 🟠 Maybe (only with mail tools) | 🟠 Maybe (only with mail tools) | 🟡 YES (but local draft is not a packet) | 🟠 Maybe (only with mail tools) | 🟠 Maybe (only with mail tools) | 🟠 Maybe (depends on the connector) | Observed |
| [**Cursor agents**](https://cursor.com) | 🟡 YES (but mail connector + poll wake) | 🟡 YES (but poll only; not a Gmail webhook) | 🟡 YES (but mail connector) | 🟢 YES | 🔴 NO (the ack is the send) | 🟡 YES (but mail connector + poll) | 🟡 YES (but on the poll path) | Operator-reported (mail + poll). Docs-claimed (cron; no Gmail trigger) |
| [**Grok Bot**](https://x.ai/bot) | 🟡 YES (but connector authorized) | 🟡 YES (but connector; webhook not claimed) | 🟡 YES (but via the connector) | 🟢 YES | 🔴 NO (the connector usually sends the ack) | 🟡 YES (but confirm with ping/pong) | 🟡 YES (but only once that send is confirmed) | Observed |
| [**SuperGrok / Grok Projects**](https://grok.com) | 🟢 YES | 🟢 YES (Observed; no webhook API claimed) | 🟢 YES | 🟢 YES | 🟢 YES | 🟢 YES | 🟢 YES | Observed |
| [**SuperGrok / Grok chat**](https://grok.com) | 🔴 NO (human must ask) | 🔴 NO (human opens chat) | 🟡 YES (but only when asked) | 🟡 YES (but only when asked) | 🟡 YES (but a chat ack is not `RES`) | 🟡 YES (but only when asked; not unattended) | 🔴 NO (human must ask) | Operator-reported (when asked). Docs-claimed (Gmail connector) |
| [**ChatGPT (Custom GPT / Actions or Work)**](https://chatgpt.com) | 🟡 YES (but pre-authorized send + seated wake) | 🟡 YES (but Gmail-event or schedule; plan/config) | 🟢 YES | 🟢 YES | 🟡 YES (but Never-ask or pre-authorized write; else it pauses) | 🟡 YES (but Work event/schedule or a Workspace Agent; not consumer web) | 🟡 YES (but only with that config; default still asks) | Observed (read, self-mail on a connected seat). Docs-claimed (Work wake) |
| [**ChatGPT consumer web**](https://chatgpt.com) | 🔴 NO (consumer web chat) | 🔴 NO (human opens chat) | 🟡 YES (but only while that chat is open) | 🟡 YES (but chat text is not a send) | 🟡 YES (but a chat ack is not `RES`) | 🔴 NO (human must approve) | 🔴 NO (human must approve) | Docs-claimed (default chat asks). Not the unattended path |
| [**Claude (MCP / Desktop)**](https://claude.com/download) | 🟡 YES (but Always-allow or schedule mode) | 🟡 YES (but schedule only) | 🟢 YES | 🟢 YES | 🟡 YES (but Always-allow or schedule mode; no self-mail example) | 🟡 YES (but same mode; ordinary chat does not send alone) | 🟡 YES (but default asks; Team/Enterprise can Always-allow) | Docs-claimed |
| [**claude.ai web**](https://claude.ai) | 🔴 NO (web chat) | 🔴 NO (human opens the page) | 🔴 NO (does not watch the inbox) | 🟡 YES (but page text is not a send) | 🔴 NO (no unattended ack) | 🔴 NO (no unattended send) | 🔴 NO (human must send) | Docs-claimed |
| [**Google Spark**](https://gemini.google.com/spark) (hub seat, beta) | 🟡 YES (but ephemeral turns; batch wake ~15–60m) | 🟡 YES (but batch ~15–60m or a human turn; operator-reported webhook, not a live socket) | 🟢 YES | 🟢 YES | 🔴 NO (the ack is the send) | 🟢 YES | 🟢 YES | Operator-reported (ephemeral turns; batch ~15–60m). Operator-reported (webhook, pending confirm). Docs-claimed (Gmail monitor) |
| [**Other Gemini seats**](https://gemini.google.com) | 🔴 NO (will not wake on inbound mail) | 🔴 NO (will not wake on inbound send) | 🟡 YES (but seat-dependent; not a wake) | 🟡 YES (but seat-dependent) | 🟡 YES (but an in-product ack is not a packet) | 🟠 Maybe (some cannot outbound send at all) | 🟠 Maybe (no packet if send does not exist) | Operator-reported (no inbound wake). Docs-claimed (Spark schedules ≠ chat actions) |
| [**Microsoft Copilot**](https://copilot.microsoft.com) (chat) | 🔴 NO (human must click Send) | 🔴 NO (human opens chat) | 🟡 YES (but often) | 🟡 YES (but often) | 🟡 YES (but the ack is not sent) | 🔴 NO (human must click Send) | 🔴 NO (human must click Send) | Operator-reported |
| [**Copilot Tasks**](https://support.microsoft.com/en-us/microsoft-copilot/using-copilot-tasks) | 🟠 Maybe (schedule; sending mail still asks) | 🟡 YES (but schedule only; not inbound mail) | 🟠 Maybe (a connector can be seated; inbox watch is not claimed) | 🟡 YES (but a task result is not a packet) | 🟡 YES (but a task result is not `RES`) | 🟠 Maybe (email send asks for approval; a scheduled run may collect that at setup; self-mail not observed) | 🟠 Maybe (setup approval is documented; not observed) | Docs-claimed (schedule and send approval) |
| [**Amazon Alexa / Alexa+**](https://alexa.amazon.com) | 🔴 NO (human must approve/send) | 🔴 NO (human must approve/send) | 🟠 Maybe (skill or wrapper) | 🟡 YES (but a spoken reply is not a packet) | 🟡 YES (but a spoken ack is not `RES`) | 🔴 NO (human must approve/send) | 🔴 NO (human must approve/send) | Operator-reported |

---

## Notes

### Generic hub

The hub requirement, not a named product. Read and send routine self-mail with no per-message click. **Assumed — verify** on the seat you use.

### [xAI Grok via OpenClaw](https://docs.openclaw.ai/providers/xai)

Bus peer when mail tools are seated. Distinct from Grok Bot, SuperGrok / Grok Projects, and SuperGrok / Grok chat. Human GO still applies outside the bus. Webhook vs poll is not claimed.

### [Open models via OpenClaw](https://openclaw.ai)

Not the Grok-on-OpenClaw result. On the bus only when mail tools are attached.

### [Cursor agents](https://cursor.com)

🟡 YES (but …) when a mail connector is seated and a routine or automation polls. That poll is unattended. It is not a Gmail webhook, and it is not “a human must open the editor.”

**Operator-reported:** read, check, and send self-mail on that poll path.

**Docs-claimed:** [Cursor Automations](https://cursor.com/docs/cloud-agent/automations) run on a cron schedule and can call MCP tools. The trigger list includes GitHub, GitLab, Slack, a generic HTTP endpoint you POST to, Linear, Sentry, and PagerDuty. It does not list a Gmail-event trigger. A generic webhook is not inbound-mail wake. Default Agent asks before an MCP tool unless Auto-review or an allowlist lets it run ([MCP](https://cursor.com/help/customization/mcp)). The poll path is the one that closes the loop.

### [Grok Bot](https://x.ai/bot)

Read and send through a connector when it is authorized. Confirm unattended self-mail with a ping/pong. Human GO outside the bus. Webhook vs poll is not claimed.

### [SuperGrok / Grok Projects](https://grok.com)

🟢 YES on the full lane: read, draft, auto-ack without Send, unattended self-mail, no Approve click. Inbound wake is 🟢 YES, **Observed**: inbound mail is processed with no human nudge. No webhook API is claimed. External mail unattended is a product capability, not a bus requirement. Bus packets stay self-mail. An unsent ack is not `RES`.

### [SuperGrok / Grok chat](https://grok.com)

Not the Projects seat. Split the columns: capability when a human asks, versus wake.

**Operator-reported:** it can read and send when asked, if a mail connector is connected. It must be asked. Wake is 🔴 NO. A human opens chat.

**Docs-claimed:** the [Gmail connector](https://docs.x.ai/grok/connectors/gmail-google-calendar) can search, read, draft, and send when send tools are enabled. [Connectors](https://docs.x.ai/grok/connectors) run inside a conversation, when the question relates to that service. Grok reads that mail in real time when you ask. That page does not claim an inbound-mail wake for chat.

Will this work? stays 🔴 NO. The hub loop has no human nudge. A chat ack is not `RES`. Do not copy the Projects wake onto this row.

### [ChatGPT (Custom GPT / Actions or Work)](https://chatgpt.com)

Not consumer web chat. **Observed:** read and self-mail on a connected seat. **Docs-claimed:** the unattended path.

Will this work only when send is pre-authorized and a wake is seated. Inbound wake is a Gmail-event trigger on eligible Work when that task is configured. A schedule is a different path. Free and Go cannot create those event tasks. If an action needs approval, the task pauses. Never-ask, or pre-authorized write, is the bypass. Workspace Agents (Business and Enterprise) can run on a schedule or an API trigger; write actions default to Always ask. Custom GPT Actions are the same class of seated path: they are not the consumer web page.

- [Connected apps](https://help.openai.com/en/articles/11487775-connected-apps-in-chatgpt)
- [Scheduled tasks](https://help.openai.com/en/articles/10291617-scheduled-tasks-in-chatgpt)
- [Workspace Agents](https://help.openai.com/en/articles/20001143-chatgpt-workspace-agents-for-enterprise-and-business)
- [Admin controls](https://help.openai.com/en/articles/11509118-admin-controls-security-and-compliance-in-connectors-enterprise-edu-and-team)
- [Release notes](https://help.openai.com/en/articles/6825453-chatgpt-release-notes)

### [ChatGPT consumer web](https://chatgpt.com)

🔴 NO on the hub loop. Default chat asks, and it does not watch the inbox. A human opening the page is not a wake. Do not copy the Work or Custom GPT row onto this one.

### [Claude (MCP / Desktop)](https://claude.com/download)

Not claude.ai web. **Docs-claimed. Not operator-observed.** Verify plan, Gmail scopes, and Always-allow.

The Gmail connector can read, draft, and send. Default is ask-before-send. Team and Enterprise can set Always allow, Needs approval, or Blocked. Will this work when send is allowed and a scheduled task’s approval mode lets it proceed. No literal self-mail example (From = To). Wake is a person-created schedule only, not a Gmail-event or webhook. Computer use is not a substitute for that schedule: the desktop must be awake and the Desktop app open.

- [Google Workspace connectors](https://support.anthropic.com/en/articles/10166901-using-the-google-drive-integration)
- [Connectors](https://support.anthropic.com/en/articles/11176164-pre-built-web-connectors-using-remote-mcp)
- [Scheduled tasks](https://support.anthropic.com/en/articles/13854387-schedule-recurring-tasks-in-claude-cowork)
- [Computer use](https://support.anthropic.com/en/articles/14128542-let-claude-use-your-computer-in-cowork)

### [claude.ai web](https://claude.ai)

🔴 NO on the hub loop. **Docs-claimed:** ordinary chat does not watch the inbox. A human opening the page is not a wake. Do not copy the MCP / Desktop row onto this one.

### [Google Spark](https://gemini.google.com/spark) (hub seat, beta)

Google Spark is a beta feature. 🟡 YES (but …), not bare 🟢 YES. It can run the protocol and use Gmail and Workspace tools. The runtime is an ephemeral turn-based container, not a real-time socket daemon. Do not apply a no-send caveat to this seat. The bus ack is the self-mail. The caveat is latency and the container, not “cannot send.”

**Operator-reported:** unattended wake is a scheduled batch, about 15–60 minutes, or a human starting a turn. A human turn is not the hub path. The batch is. That interval is not a number from the vendor page.

**Operator-reported, pending confirm:** inbound mail can webhook. That claim does not make this a live socket. Do not record this seat as “no HTTP webhook.” A second operator has not confirmed the webhook here.

**Docs-claimed:** [Spark schedules](https://support.google.com/gemini/answer/17094710) include a Gmail monitor that runs when a message matches a Gmail filter. That article does not name an HTTP webhook, and it does not describe a socket daemon. Spark schedules are a different feature from scheduled actions in Gemini chat.

Canonical state does not live on the turn’s scratch disk. See [State backing](#state-backing).

### [Other Gemini seats](https://gemini.google.com)

Not the Spark hub seat. **Operator-reported:** they will not wake on an inbound send. **Docs-claimed:** the Spark help page above keeps Spark schedules separate from scheduled actions in Gemini chat. It does not give non-Spark Gemini a Gmail monitor.

Some of these seats still cannot outbound send at all. Those need a thin sender peer. Google Keep, Tasks, and Reminders are not the wire. Reading or sending while a chat is open is not a wake.

### [Microsoft Copilot](https://copilot.microsoft.com) (chat)

Not [Copilot Tasks](#copilot-tasks). **Operator-reported.** Often reads, drafts, and auto-acks. Cannot send until a human clicks Send, including self-mail. The draft looks done. The bus sees silence. Opening the chat is not a wake.

### [Copilot Tasks](https://support.microsoft.com/en-us/microsoft-copilot/using-copilot-tasks)

Not Copilot chat. **Docs-claimed.** [Using Copilot Tasks](https://support.microsoft.com/en-us/microsoft-copilot/using-copilot-tasks).

A task runs once when you submit it, or on a schedule you set. Tasks do not start unless you ask to start one. A schedule is not inbound-mail wake. That page does not claim a Gmail-event or webhook.

Sending an email asks for approval, or hands control back. For a scheduled or recurring task, Copilot may collect that approval during setup so a later run can proceed. That does not remove the operator-reported Send-click on Copilot chat, and it is not an observed self-mail. Will this work? stays 🟠 Maybe until a ping/pong shows the packet leaves with no per-message click.

Personal Copilot accounts are moving from Tasks to [Copilot Cowork](https://support.microsoft.com/en-us/microsoft-365-copilot/get-started-with-cowork). Cowork is the same path: you review and approve sensitive actions, including sending mail. It is not a second verdict.

### [Amazon Alexa / Alexa+](https://alexa.amazon.com)

**Operator-reported.** A spoken or in-app ack is not `RES`. A human must finish approve/send, including self-mail. No mail webhook is claimed.

---

## State backing

The mailbox packet is not the hub’s memory. Name the canonical store. Three kinds:

| Store | When it is the store |
|-------|----------------------|
| Local SQLite, or another file on disk | The process stays up and keeps that file |
| Workspace Drive, or Keep used only as a store | An ephemeral turn container. The Spark hub seat is in this set |
| External database | A seat you run, when a local file is the wrong boundary |

An ephemeral container needs Workspace-backed state. Drive is the usual store. Keep may hold state. Google Keep, Tasks, and Reminders are still not the wire. The wire stays self-mail on the one shared mailbox.

---

## Bridging Maybe/NO seats via workflow tools

A workflow tool can bridge a Maybe or NO seat when that seat cannot wake on mail or cannot send on its own. The tool is not the hub. Prefer self-hosted n8n. Zapier and Make are other examples. Their docs and demos:

- **n8n.** [Gmail Trigger](https://docs.n8n.io/integrations/builtin/trigger-nodes/n8n-nodes-base.gmailtrigger) · [Webhook](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.webhook) · [Gmail Trigger workflows](https://n8n.io/integrations/gmail-trigger/) · [Webhook and Gmail workflows](https://n8n.io/integrations/webhook/and/gmail/)
- **Zapier.** [Gmail](https://help.zapier.com/hc/en-us/articles/8495933589645-How-to-get-started-with-Gmail-on-Zapier) · [Webhooks](https://help.zapier.com/hc/en-us/articles/8496288690317-Trigger-Zap-workflows-from-webhooks) · [New email matching a search](https://zapier.com/apps/gmail/integrations/google-docs/1618213/create-documents-from-template-in-google-docs-for-new-matching-emails-in-gmail) · [Send Gmail from a webhook](https://zapier.com/apps/gmail/integrations/webhook/1755/send-an-email-in-gmail-for-new-webhooks)
- **Make.** [Gmail modules](https://apps.make.com/gmail-modules) · [Webhooks](https://help.make.com/webhooks) · [New Gmail email](https://www.make.com/en/templates/13525-trigger-new-emails-in-gmail-and-add-rows-to-google-sheets-for-tracking) · [Webhook to Gmail](https://www.make.com/en/templates/13453-send-automated-email-notifications-using-custom-webhooks-and-google-email)

---

## Do not

| Mistake | Do instead |
|---------|------------|
| Treating a mailbox plugin as unattended self-mail | Ping/pong. Approval-gated seats stay human-in-the-loop |
| Treating read or self-mail as a wake | Use the wake column. Human-open-chat is 🔴 NO |
| Treating a schedule as a Gmail-event wake | Schedule-only stays 🟡 YES (but …). A poll routine is still a wake |
| Treating Cursor as editor-open only | With a mail connector, wake is poll only. Not a Gmail webhook |
| Treating Grok chat as a no-send path | It can read and send when asked. Wake stays 🔴 NO |
| Copying the Projects wake onto Grok chat | Chat does not wake. Projects is the other row |
| Treating read, draft, or auto-ack as `RES` | The self-mail has to leave |
| Recording Spark as “no HTTP webhook” | Wake is 🟡 YES (but batch or a human turn). The webhook stays operator-reported, pending confirm, and is not a live socket |
| Treating the Spark hub as a real-time 🟢 YES | It is 🟡 YES (but ephemeral turns and batch latency). It can still send |
| Treating the Spark hub seat as a no-send Gemini seat | Spark can send. Other Gemini seats will not wake on inbound send |
| Treating other Gemini like the Spark hub | They do not wake on inbound send. A thin sender peer is only for seats that also cannot send |
| Copying ChatGPT Work onto consumer web | Consumer web is 🔴 NO. Work, Custom GPT, or Actions is the 🟡 YES (but …) row |
| Copying Claude MCP / Desktop onto claude.ai | claude.ai web is 🔴 NO. MCP / Desktop is the 🟡 YES (but …) row |
| Copying Copilot Tasks onto Copilot chat | Chat stays 🔴 NO (human must click Send). Tasks is the schedule row |
| Treating Copilot Tasks as unattended self-mail | Sending mail still asks for approval. A setup-time approval is not an observed self-mail |
| Keeping canonical state on an ephemeral turn disk | Use Workspace-backed state. Keep is a store, not the wire |
| Treating a workflow wrapper as the hub | The wrapper moves packets. Ping/pong the seat |
| Using Google Keep, Tasks, or Reminders as the bus | Self-mail on the one shared mailbox |
| Treating an Alexa utterance as `RES` | Wait for the self-mail |
| Copying one family’s send path onto another | Verify the seat |
| Treating a docs-claimed routine as Observed | Label it docs-claimed. Re-check the seat |

Unnamed products stay observers until a ping/pong passes and the wake path is known.

## Hub rule

A hub must send routine self-mail unattended, and inbound mail must wake it with no human nudge. A scheduled batch counts. A human turn does not. Read, draft, auto-ack, and “can self-mail after someone opens the chat” do not qualify. A seat that cannot outbound send needs a thin sender peer. An ephemeral turn container needs Workspace-backed state. Human GO still applies outside the bus. See [`00-stand-up-order.md`](00-stand-up-order.md).
