---
name: price-correction
description: "Work out the credit note when a contract or a renewal was priced too high, and what happens to revenue month by month. Only calculates; nothing in defrr is changed."
disable-model-invocation: true
---

Work out a price correction (a credit for a price that was too high) using `defrr_reprice_contract` for the original term, or `defrr_reprice_renewal` for the latest renewal. These only calculate: nothing is saved or posted. Each call costs 2 credits, refunded if it fails.

Anything I have given you after the command is what I have so far. If I have given you nothing, ask me which customer and which term.

Steps:

1. Get the contract's terms. If I name a customer, find them with `defrr_find_contracts` and read the contract with `defrr_get_contract`, and use the `schedule` it returns exactly as it is (if it has a foreign currency, keep it). If it returns no schedule, say why and ask me for the terms. If I give terms myself, ask for each term's start date, end date and price, and whether end dates are included.
2. Work out which term is being corrected. The original term uses `defrr_reprice_contract`. A renewal uses `defrr_reprice_renewal`, and only the **latest** renewal can be corrected: `termIndex` 1 is the first renewal, 2 the second, and so on, and it must be the last term. A middle renewal can't be corrected this way: say so.
3. Ask what that term's price **should have been** (its total price, in the contract's own currency). It must be lower than the current price. A higher price is not handled by these tools: say so.
4. Closed months: if you are connected to an organization, use `defrr_get_workspace` and pass its locked period as `lockedThrough` (YYYY-MM). Otherwise ask me for the latest closed month. Revenue in a closed month is never changed: the difference is posted in the next open month.
5. Show me the result plainly: the credit note, the revenue to reverse for closed months and the month it is posted in, and the corrected revenue by month. Say clearly that nothing has been changed in defrr: a person posts the correction in defrr, and the in-app assistant can prepare it for them to confirm.
