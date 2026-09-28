# Peer / agent capability matrix

Operators need to know which product families can **close the hub loop** (read mail on the shared mailbox and send self-mail) **without a human click per message**. Approval-gated senders stall `RES` and stop unattended bus traffic even when they draft well.

**This table goes stale.** Connectors change. Treat every “unattended send” claim as **verify before you rely on it**. Run a live ping/pong on the seated wire tag before trusting a seat as hub or as an unattended peer.

Related: [`00-stand-up-order.md`](00-stand-up-order.md), [`faq-anti-patterns.md`](faq-anti-patterns.md), [`threat-model.md`](threat-model.md).

Placeholders only: `[CATBUS]`, `@example.com`. No live mailboxes, tags, or seating maps.

**Transport reminder:** bus packets are self-mail on one shared mailbox (From = To = that mailbox). A mailbox rule on the subject wire tag keeps that traffic out of the ordinary human inbox. This matrix is about who can perform that self-mail. It is not a guide to mailing other people. A separate cross-hub protocol exists and is not specified here.

---

## Matrix

| Product family | Hub role fit | Read hub mail | Send self-mail unattended? | Send needs human approve (including self-mail)? | Notes | Evidence |
|----------------|--------------|---------------|----------------------------|--------------------------------------------------|-------|----------|
| **Generic shared-mailbox central agent** | **Hub** (preferred fit) | Yes (required) | **Must** for routine hub traffic | No for routine self-mail; **yes** for destructive acts and mail to anyone other than the shared mailbox | Preferred hub seat: read and send the shared mailbox without a per-message click for routine self-mail. Otherwise the human becomes the send queue and the bus is not unattended | Assumed — verify |
| **xAI Grok via OpenClaw** (local OpenClaw hosting Grok) | Hub or peer (when mail tools are seated) | Yes when read tools are wired | Yes when send tools are seated and allowed unattended | Policy may still require human GO for destructive acts and mail outside the bus | Bus peer when send and read tools are wired. Distinct from the Grok Bot product and from SuperGrok chat | Observed |
| **Open models via OpenClaw** (local open weights on OpenClaw) | Worker-strong peer; hub only if mail tools are attached | Only if mail tools are attached | Only if mail tools are attached | Depends on the connector when a mail path exists | Separate from Grok-on-OpenClaw. On the bus only when mail tools are attached; otherwise another mail-capable peer has to carry packets | Observed |
| **Cursor agents** (IDE and cloud agents) | Peer (with a mail path) or observer | No native mailbox unless a connector is seated | No (unless a mailbox connector is seated) | Depends on the connector | Often no native mailbox access. Use a connector, a mail-capable peer, or human paste. Keep transport off the IDE agent unless send and read are seated | Observed |
| **Grok Bot** (xAI Grok Bot product with connectors, for example Gmail) | Hub or peer (when the connector is authorized) | Typically yes via the connector | Often yes for self-mail when the connector is authorized | Connector may send; human GO for mail outside the bus is appropriate | Distinct from OpenClaw-hosted Grok and from SuperGrok / Grok chat. Confirm unattended self-mail before hub duty | Observed |
| **SuperGrok / Grok chat** | Observer (unless a send path is added) | No mailbox connector by default | No (chat product) | Not applicable without a send path | A chat product without a mailbox connector stays an observer. Do not conflate it with Grok Bot | Assumed — verify |
| **Google Gemini** (agents on Google Workspace) | Peer (limited), or needs a sender peer | Often yes (seat-dependent) | **Often no** on some seats | Seat-dependent; some seats cannot send at all | Some seats need another sender or a human for outbound self-mail. Workspace side panels are not the CATBus wire | Observed / operator-reported |
| **Microsoft Copilot** (personal Microsoft 365 with Gmail) | **Peer** (human in the loop) or reader | Often yes | **No** (typical) | **Yes — including self-mail to the shared mailbox** | Expect stalled `RES` until a human clicks Send / Approve. Usable as a reader or as a peer if a human stays in the send loop. Do not seat as the only hub for unattended traffic | Operator-reported |
| **Amazon Alexa / Alexa+** | Observer-biased, or limited peer until verified | Via a skill or wrapper (varies) | **No** (typical) | **Yes — including self-mail** | Approve even self-mail. Treat as a limited peer only after unattended send is verified | Operator-reported |

---

## Anti-pattern

| Anti-pattern | Why it fails | Do instead |
|--------------|--------------|------------|
| Assuming every model with a mailbox plugin can **send self-mail unattended** | Many products gate Send / Approve even for self-mail to the shared mailbox. No `RES` arrives, correlation looks dead, and the peer looks down | Use this matrix, run a self-mail ping/pong, and keep approval-gated seats as readers or human-in-the-loop peers |

---

## Checklist before seating

1. Can this seat **read** mail that carries the wire tag without a human paste each time?
2. Can it **send self-mail to the same shared mailbox** without a per-message human Approve / Send click?
3. If (2) is no: mark it observer or human-in-the-loop peer. Do not depend on it for hub liveness.
4. Mail to other people, spends, and irreversible infrastructure still need human **GO** from the hub even when unattended self-mail works ([`00-stand-up-order.md`](00-stand-up-order.md)).
5. Confirm a mailbox rule on the wire tag is armed so bus traffic does not bury the human inbox.
6. Re-check after product or connector updates.

## Hub candidate rule

A hub candidate **must** send routine self-mail unattended. Otherwise the human is the send queue and the bus is not unattended. See prerequisites in [`00-stand-up-order.md`](00-stand-up-order.md).
