# Peer / agent capability matrix

You already know your AI agent fleet before stand-up. This matrix is the up-front check for whether CATBus fits that fleet.

Operators need to know which product families can **close the hub loop** (read mail on the shared mailbox and send self-mail) **without a human click per message**. Approval-gated senders stall `RES` and stop unattended bus traffic even when they draft well. A seat that can read, draft, or auto-ack, and still cannot send, fails the same way.

**This table goes stale.** Connectors change. Treat every “unattended send” claim as **verify before you rely on it**. Run a live ping/pong on the seated wire tag before trusting a seat as hub or as an unattended peer.

Related: [`00-stand-up-order.md`](00-stand-up-order.md), [`faq-anti-patterns.md`](faq-anti-patterns.md), [`threat-model.md`](threat-model.md).

Placeholders only: `[CATBUS]`, `@example.com`. No live mailboxes, tags, or seating maps.

**Transport reminder:** bus packets are self-mail on one shared mailbox (From = To = that mailbox). A mailbox rule on the subject wire tag keeps that traffic out of the ordinary human inbox. This matrix is about who can perform that self-mail. It is not a guide to mailing other people. A separate cross-hub protocol exists and is not specified here.

---

## How to read a row

| Column | Meaning |
|--------|---------|
| **Hub fit** | Whether this family can be the shared-mailbox central agent for routine bus traffic |
| **Read unattended** | Can it read wire-tagged mail on the shared mailbox without a human paste each time? |
| **Draft** | Can it compose a reply or a packet? A draft is not on the wire |
| **Auto-ack without Send** | Can it prepare or surface an acknowledgement **without** a self-mail message leaving? That ack is **not** a bus `ack` or `RES` |
| **Send self-mail unattended** | Can it send to the same shared mailbox with no per-message human click? |
| **Needs human Approve for self-mail** | Does routine self-mail (From = To = the shared mailbox) still wait for a human Approve / Send? |
| **Evidence** | **Observed** — an operator has seen this on a seat of that family. **Operator-reported** — an operator reported it; this repository does not re-demonstrate it. **Assumed — verify** — no operator evidence is recorded here |

Cell values are the working picture for that family. They are not a certificate for your seat. Re-check after product or connector updates.

**Thin sender peer:** a second seat that performs the self-mail send for a seat that can read or draft but cannot put a packet on the shared mailbox. The sender peer still sends only to that same mailbox.

---

## Matrix

| Product family | Hub fit | Read unattended | Draft | Auto-ack without Send | Send self-mail unattended | Needs human Approve for self-mail | Evidence |
|----------------|---------|-----------------|-------|------------------------|---------------------------|-----------------------------------|----------|
| **Generic shared-mailbox central agent** | **Hub** (preferred fit) | Yes (required) | Yes | No. The ack is the self-mail send | **Must**, for routine hub traffic | No for routine self-mail. Yes for destructive acts and mail outside the bus | Assumed — verify |
| **xAI Grok via OpenClaw** (local OpenClaw hosting Grok) | Hub or peer, when mail tools are seated | Yes, when read tools are wired | Yes | No separate ack-without-send when unattended send is allowed. The bus ack is the self-mail | Yes, when send tools are seated and allowed unattended | No for routine self-mail in that setup. Human GO still applies outside the bus | Observed |
| **Open models via OpenClaw** (local open weights on OpenClaw) | Worker-strong peer. Hub only if mail tools are attached | Only if mail tools are attached | Yes locally. A bus packet still needs the mail path | Only if mail tools are attached and unattended send is allowed | Only if mail tools are attached | Depends on the connector when a mail path exists | Observed |
| **Cursor agents** (IDE and cloud agents) | Peer with a mail path, or observer | No, unless a mailbox connector is seated | Yes in the editor. That draft is not a bus packet | No, unless a connector actually sends | No, unless a mailbox connector is seated and allowed to send | Depends on the connector | Observed |
| **Grok Bot** (xAI Grok Bot product with connectors, for example Gmail) | Hub or peer, when the connector is authorized | Typically yes, via the connector | Yes | Often the connector sends the self-mail ack when authorized. Confirm before hub duty | Often yes for self-mail when the connector is authorized | No for routine self-mail when that is confirmed. Human GO for mail outside the bus | Observed |
| **SuperGrok / Grok Projects** | **Yes.** Hub or peer | **Yes** | **Yes** | **Yes** | **Yes** | **No** | Observed |
| **SuperGrok / Grok chat** (chat / chatObserver) | Observer | No mailbox connector by default | Yes in chat. Not a bus send | No | No | Not applicable without a send path | Assumed — verify |
| **Google Gemini / Spark-class** (agents on Google Workspace) | Limited peer, or needs a thin sender peer. Not a hub when outbound send is missing | Often yes (seat-dependent) | Often yes | An in-product ack is not a wire packet. A seat that cannot send never closes the loop alone | **Often no.** Some seats **cannot outbound send at all** | Seat-dependent. Where outbound send does not exist, a human click still does not produce a packet | Operator-reported |
| **Microsoft Copilot** (personal Microsoft 365 with Gmail) | Human-in-the-loop peer, or reader. **Not** the sole hub | **Often yes** | **Often yes** | **Often yes.** It can read and prepare an acknowledgement. That ack is **not sent** | **No.** It cannot reply or send until a human clicks **Send** | **Yes — including self-mail** to the shared mailbox. The human must click Send | Operator-reported |
| **Amazon Alexa / Alexa+** | Observer-biased. Limited peer only after unattended send is verified | Via a skill or wrapper (varies) | May surface a reply. That reply is not a bus packet | A spoken or in-app acknowledgement is **not** `RES` | **No** (typical) | **Yes — including self-mail.** A human must complete approve/send | Operator-reported |

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

Hub or peer. Read unattended, draft, auto-ack without Send, and send self-mail unattended. An auto-ack that does not leave as self-mail is not a bus `RES`. Routine self-mail does not wait on a human Approve. Distinct from Grok Bot, from Grok on OpenClaw, and from SuperGrok / Grok chat. It can also send external mail unattended. External send is a product capability, not a bus requirement. Bus packets stay self-mail on the shared mailbox. Observed. Re-check with a self-mail ping/pong before hub duty.

### SuperGrok / Grok chat

Chat / chatObserver with no mailbox send path stays an observer. A draft in chat is not a bus send. Do not use this row for a Projects seat. **Assumed — verify** until that path shows a real self-mail send.

### Google Gemini / Spark-class

Operator-reported for Gemini agents on Google Workspace, including Spark-class seats. Read and draft are often available and depend on the seat. **Some seats cannot outbound send at all.** No self-mail packet is produced, with or without a human click. Those seats need a **thin sender peer**.

Workspace Keep, Tasks, Reminders, and similar side channels are not self-mail on the shared mailbox. They are not the CATBus wire. Do not seat a no-send seat as the hub.

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

## Other common chat products (no operator evidence)

These rows exist so a common name is not mistaken for a tested seat. They are not extra product families with known send behavior. Do not copy another row onto them. Do not invent connector details. Until a self-mail ping/pong passes on that seat, treat it as an observer.

| Product family | Working assumption | Evidence |
|----------------|--------------------|----------|
| **Anthropic Claude** (chat, projects, or desktop) | No operator evidence here for unattended read or unattended self-mail. Connectors vary by seat | Assumed — verify |
| **OpenAI ChatGPT** (chat or desktop) | No operator evidence here for unattended read or unattended self-mail. Connectors vary by seat | Assumed — verify |

Add a family to the main matrix only with an evidence label and a re-checkable claim. Leave speculative detail out.

---

## Anti-pattern

| Anti-pattern | Why it fails | Do instead |
|--------------|--------------|------------|
| Assuming every model with a mailbox plugin can **send self-mail unattended** | Many products gate Send / Approve even for self-mail to the shared mailbox. No `RES` arrives, correlation looks idle, and the peer looks down | Use this matrix, run a self-mail ping/pong, and keep approval-gated seats as readers or human-in-the-loop peers |
| Counting **read, draft, or auto-ack** as `RES` (Copilot-class) | The product answered locally. The self-mail never left, because a human has not clicked Send — including when the recipient is the shared mailbox | Require the message on the shared mailbox. If Send is mandatory, mark the seat human-in-the-loop |
| Seating a **Gemini / Spark-class** seat that cannot outbound send as the only sender | Nothing can emit `RES`. A human click does not create a packet the product cannot send | Pair a thin sender peer that can perform the self-mail |
| Using Workspace **Keep, Tasks, Reminders**, or similar side channels as the bus | Those surfaces are not self-mail on the shared mailbox. They are not the wire | Send and read the one shared mailbox. Arm the mailbox rule on the wire tag |
| Treating a spoken or in-app **Alexa** acknowledgement as `RES` | The skill can look done while approve/send, including self-mail, is still waiting on a human | Wait for the self-mail packet, or keep the seat observer-biased |
| Copying one family’s send behavior onto another, including the Assumed — verify rows | Chat products and connector seats do not share a send path | Verify the seat you are about to use |

---

## Checklist before seating

1. Can this seat **read** mail that carries the wire tag without a human paste each time?
2. Can it **draft**? If yes, is that draft still off the wire until something sends it?
3. Can it **auto-ack without Send**? If yes, that acknowledgement is not a bus `RES`.
4. Can it **send self-mail to the same shared mailbox** without a per-message human Approve / Send click?
5. If send needs a human click **including self-mail**: mark observer or human-in-the-loop peer. Do not depend on it for hub liveness.
6. If the seat **cannot outbound send at all**: name a thin sender peer before depending on it for `RES`.
7. Confirm Workspace side channels (Keep, Tasks, Reminders, and similar) are not standing in for the wire.
8. Mail to other people, spends, and irreversible infrastructure still need human **GO** from the hub even when unattended self-mail works ([`00-stand-up-order.md`](00-stand-up-order.md)).
9. Confirm a mailbox rule on the wire tag is armed so bus traffic does not bury the human inbox.
10. Re-check after product or connector updates. Public examples use `[CATBUS]` and `@example.com`.

## Hub candidate rule

A hub candidate **must** send routine self-mail unattended. Otherwise the human is the send queue and the bus is not unattended. Read, draft, and auto-ack do not satisfy this rule. A seat that cannot outbound send does not satisfy it. See prerequisites in [`00-stand-up-order.md`](00-stand-up-order.md).
