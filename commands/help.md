---
name: help
description: "List what the defrr commands do and when to use each one. Free: it calls no tool."
---

Show me this list and nothing else. Do not call any tool, so it costs no credits. If I have asked about a particular task, say which command fits it.

**Guided commands** (each asks for anything missing, then reads the terms back before it acts):

- `/defrr:new-contract`: save a new contract to defrr as a draft. Use it when a deal is signed.
- `/defrr:renewal`: find a customer's contract and save its renewal as a draft. Use it when a customer renews.
- `/defrr:price-correction`: work out the credit note when a contract or its latest renewal was priced too high, and how revenue changes month by month. It only calculates.
- `/defrr:cancellation`: show what cancelling a contract, or its latest renewal, would do. It only calculates: nothing is cancelled.
- `/defrr:recognition-schedules`: show billing, revenue and the deferred revenue balance month by month for one or more contracts, with a total.

**Or just ask in plain English**, for example "What does Acme's contract recognise each month?" or "Create a contract for Acme Corp, 1 Jan to 31 Dec at $12,000". Claude picks the right tool.

**Good to know**
- Saving contracts and renewals needs an API key with **Save contracts in Defrr** ticked. A calculate-only key stores nothing.
- Saved contracts and renewals are drafts: they stay out of every report until a person approves them in defrr.
- Each calculation costs credits (shown on the tool), and a call that fails is refunded.
- Keys and credits are at https://defrr.com/developers/account. Full guide: https://defrr.com/developers/guide.
