---
name: cancellation
description: "Work out what cancelling a contract, or its latest renewal, would do: the credit note and the revenue reversed, month by month. Only calculates; nothing is cancelled."
---

Work out what a cancellation would do using `defrr_cancel_contract` (a whole contract) or `defrr_cancel_renewal` (the latest renewal, in full). These only calculate. **Nothing is cancelled**: the person cancels in defrr. Each call costs 2 credits, refunded if it fails.

Anything I have given you after the command is what I have so far. If I have given you nothing, ask me which customer and whether it is the whole contract or the latest renewal.

Steps:

1. Get the contract's terms. If I name a customer, find them with `defrr_find_contracts` and read the contract with `defrr_get_contract`, and use the `schedule` it returns exactly as it is. If it returns no schedule, say why and ask me for the terms. If I give terms myself, ask for each term's start date, end date and price, and whether end dates are included.
2. For a whole contract use `defrr_cancel_contract`. For a renewal use `defrr_cancel_renewal` with the `termIndex` of the **latest** renewal (1 is the first renewal, 2 the second). Only the latest renewal can be cancelled this way: say so if I ask for an earlier one.
3. Closed months: if you are connected to an organization, use `defrr_get_workspace` and pass its locked period as `lockedThrough` (YYYY-MM). Otherwise ask me for the latest closed month. Revenue in a closed month is reversed in the next open month, never changed in place.
4. Show me the result plainly: the credit note, the revenue reversed for closed months and the month it lands in, and the revenue that remains by month. Say clearly that this is what a cancellation **would** do and that nothing has been changed: a person cancels in defrr, and the in-app assistant can prepare it for them to confirm.
