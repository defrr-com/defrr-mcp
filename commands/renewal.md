---
name: renewal
description: "Save a customer's renewal to defrr as a draft. Finds their contract, asks for the new end date and price, reads it back for you to confirm, then saves it."
---

Save a renewal for a contract that is already in defrr, using the `defrr_save_renewal` tool. This needs an API key with **Save contracts in Defrr** ticked. If the tool isn't available, say so and point to https://defrr.com/developers/account.

Anything I have given you after the command is what I have so far (usually the customer's name). If I have given you nothing, ask me which customer renewed.

Steps:

1. Find the customer's contract with `defrr_find_contracts`. If several match, list them briefly and ask which. Then read it with `defrr_get_contract`. If it returns no schedule, tell me why in one line and stop: that renewal is done in defrr itself.
2. Show me the contract's current terms as a short list (dates, price, currency if it is not the organization's own) so I can see what is being renewed.
3. Ask for what is missing, one short question at a time:
   - the renewal's **end date**, meaning the last day it covers. Follow the contract's own convention (the `endDateIncluded` in its schedule): if it is `true`, send the last covered day itself; if it is `false`, send the day after;
   - the renewal term's **total price**. It is the whole term's price, not an increase and not a monthly rate;
   - if the contract bills by units: how many units the new price covers (if I don't say, it keeps the units of the term before it, so tell me that);
   - if the contract is priced in a foreign currency: send `currency` and the price in it. If I give no exchange rate, defrr uses its daily rate for the renewal's start date, so say which rate and date it used;
   - optionally a reference and where the renewal came from.
4. Before you save, read the renewal back as a short list and wait for me to confirm. Do not guess dates or prices.
5. Save it with `defrr_save_renewal`. Then tell me which organization it was saved to, the draft ID, the new term's dates and price, and that it stays a **draft** until it is approved in defrr under Contracts, Draft renewals. Only approve it yourself if I ask and the key allows it.
6. If defrr refuses (for example the renewal would start in a closed month), tell me the reason as it was given and that the person handles it in defrr. Do not try to work around it.
