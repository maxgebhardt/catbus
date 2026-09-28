# FAQ / anti-patterns

## FAQ

### Do I need to learn CATBus?
No. Hand [`00-stand-up-order.md`](00-stand-up-order.md) to the shared-mailbox central agent that can send and read the owner mailbox. It interviews you, configures itself, mints hub and peer cards, and maintains topology. You answer short questions and say **GO** only when that agent asks.

### When do I say GO?
Only when the hub asks: stand-up and mint confirmation, a breaking protocol change, or a destructive or send-authorization action (mail to someone other than the shared mailbox, spends, irreversible infrastructure). You do not study the protocol to find those moments.

### What is CATBus?
An open protocol for serverless, asynchronous tasking and telemetry among agents over email. No broker. The specification in this repository is free to use (MIT). Seated answers (wire tag, callsigns) stay in hub local state. They are not published here.

### Is bus mail sent to other people?
No. Bus traffic is **exclusively self-mail on one shared mailbox**: **From = To = the owner mailbox** (Gmail or another mailbox the agents can access). `target` is a callsign inside the JSON, not an SMTP recipient.

A separate cross-hub protocol exists. This FAQ and the stand-up do not describe it. Do not invent cross-hub routing from these pages.

### Why a mailbox filter?
So bus and bot traffic does not bury the human inbox. Create a rule whose condition is the subject wire tag (demo pattern: subject contains `[CATBUS]`, or a Gmail-style query `subject:[CATBUS]`). You still have access to that mail for review. Tag match is routing, not authentication.

### Do I design who sits where?
No. You do not draw a permanent org chart. Agents may specialize on the bus within sterile-bus rules and GO. You review the traffic. The hub keeps the topology current. Seating a new or unknown sender still needs your explicit yes.

### How do I stand up a hub?
Paste [`00-stand-up-order.md`](00-stand-up-order.md) into the shared-mailbox central agent. If seating is unknown, its first message is the interview opener in that document. It mints cards only after the summary in that interview and your **yes**. See [`../prompts/hub.md`](../prompts/hub.md).

### Is the subject tag authentication?
No. Tags route, and they drive the mailbox rule. Checks are transport alignment, seating, optional signatures after keys are seated, and human GO when the hub asks.

### Must I use signing?
Optional. Recommended after keys are seated **off the bus**. Signing stays **off** until you confirm that. [`signing.md`](signing.md).

### Should I use opaque secure envelopes?
Optional, and **advised against**. They hide content from human review. Prefer sterile cleartext plus signing. [`secure-envelope.md`](secure-envelope.md).

### Is the threat model optional?
No. It is **MUST** before production peers. A lab may stay on ping/pong until you accept that path. [`threat-model.md`](threat-model.md).

### Can peers invent events?
No. The hub owns the protocol.

### Can the hub seat example callsigns such as `orchestrator` on its own?
No. Examples are suggestions. Each live callsign and the wire tag need your explicit yes.

### What protocol pin does the hub propose?
The `version` and `envelope_v` in [`../schema/protocol-version.json`](../schema/protocol-version.json). As of 0.3.2 that is protocol **0.3.2** and envelope **`v: "2"`**. The hub waits for your yes. It does not invent another pin. After you accept, seated identity uses those answers.

### Which wire tag appears in these docs?
`[CATBUS]` is the public demo tag. A production tag is one you invent and do not publish.

### Why is a peer not answering on the bus?
Some products can read mail and even auto-ack (prepare an acknowledgement) but still cannot reply or send until a human clicks Send. That includes self-mail to the shared mailbox. Some seats cannot outbound send at all. Then `RES` never arrives. Treat them as readers or human-in-the-loop peers, or pair a thin sender peer. See [`peer-capability-matrix.md`](peer-capability-matrix.md).

### Does valid JSON or a valid signature authorize spends or mail to other people?
No. The hub asks for human GO. Mail to other people is not bus traffic.

### Do cards include my mailbox address?
No. Cards say the shared mailbox is human-configured. They never print the real address.

## Anti-patterns

| Anti-pattern | Do instead |
|--------------|------------|
| Expecting humans to study the protocol | The hub asks for GO at known gates |
| Hand-drawing a permanent seating chart | Let seating change under hub policy; human reviews traffic; unknown senders still need a human yes |
| Skipping the interview opener when nothing is seated | Use the opener in the stand-up and in [`../prompts/hub.md`](../prompts/hub.md) |
| Inventing a protocol pin or seating without a yes | Propose the pin in `schema/protocol-version.json`; wait |
| Minting cards before the summary and a human yes | Hard mint gate |
| Ignoring the envelope `v` the human accepted | Use the interview values |
| Seating example callsigns or `[CATBUS]` without a yes | Human yes for the tag and each callsign |
| Turning signing on before keys are seated | Stay off; keys and ceremony stay off the bus |
| Putting a real mailbox address on cards | “Human-configured” only |
| Treating `[CATBUS]` as production authentication | Private tag if you need one; seating; optional signatures |
| Secrets or `.env` on the bus | `ref_id` only; secret store off the bus |
| Several fenced blocks in one body | One fence; sparse packets |
| Peer-invented events | Hub vocabulary only |
| Applying breaking protocol changes automatically | Hub asks for GO |
| Opaque envelopes by default | Cleartext plus signing |
| Publishing live tags, callsigns, or addresses | Placeholders in public text; real seating stays unpublished |
| Schema validation treated as trust | Policy plus human GO |
| Reusing `correlation_id` across tasks | Fresh UUID per initiation |
| Skipping the threat model for production peers | Required |
| GO that exists only inside ciphertext | Cleartext GO |
| Jargon on minted cards | Plain fields from the stand-up templates |
| Peer card missing protocol pin or envelope `v` | Include both from the interview |
| Assuming every mail-capable agent can send self-mail unattended | Verify; see [`peer-capability-matrix.md`](peer-capability-matrix.md) |
| Counting a draft or an auto-ack as `RES` | The packet has to be self-mail on the shared mailbox. A Send click still required for self-mail means the seat is human-in-the-loop |
| Treating Workspace Keep, Tasks, or Reminders as the bus | Side channels are not the wire. Self-mail on the one shared mailbox only |
| Sending bus packets to external recipients | Self-mail only; From = To = the one shared mailbox |
| Standing up cross-hub routing from these docs | Out of scope here; a separate protocol exists and is not specified in this repository |

## Related

- Stand-up: [`00-stand-up-order.md`](00-stand-up-order.md)
- Peer send limits: [`peer-capability-matrix.md`](peer-capability-matrix.md)
- Threat model: [`threat-model.md`](threat-model.md)
- Security summary: [`security.md`](security.md)
