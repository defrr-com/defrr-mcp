---
name: new-contract
description: Save a new contract to your defrr organization as a draft. Asks for anything missing, reads the terms back for you to confirm, then saves it.
---

Save a new contract to defrr as a draft, using the `defrr_save_contract` tool (not `defrr_create_contract`). This needs an API key with **Save contracts in Defrr** ticked. If the tool isn't available, say so and point to https://defrr.com/developers/account.

Anything I have given you after the command is what I have so far. If I have given you nothing, ask me for the details.

What you need before saving:

- the customer's name and the product or plan;
- for each term, its start date, end date and price (the original term first, then any renewals);
- whether revenue is recognised `exact` (per day) or `smooth` (equal months). If I don't say, use the organization's default (`exact` is `exact`, `advance` is `smooth`) and tell me which you used;
- if I have them: a reference (a PO or quote number) and where the terms came from.

Rules:

1. Ask me for anything that is missing or unclear, one short question at a time. Do not guess dates or prices.
2. If the dates run like 1 Jan to 31 Dec, the end date is the last day of the term (`endDateIncluded: true`). Ask me if you can't tell.
3. Before you save, read the terms back as a short list and wait for me to confirm.
4. After saving, tell me which organization it was saved to, the draft ID, the total price, the first and last revenue month, and any warning about closed or past months (`inThePast`). Remind me that it stays a draft until it is approved in defrr under Contracts, Draft contracts, or with `defrr_approve_draft` if my key can approve.
