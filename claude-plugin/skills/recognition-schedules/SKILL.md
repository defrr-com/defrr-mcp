---
name: recognition-schedules
description: "Show the month-by-month billing, revenue and deferred revenue balance for one or more contracts, with a total across them. Asks for the contracts, then works out the schedules."
disable-model-invocation: true
---

Work out revenue recognition schedules for one or more contracts: for each contract and for all of them together, every month's billing, revenue and ending deferred revenue balance (opening deferred balance + billing - revenue = ending). Use `defrr_create_waterfall` for several contracts (up to 50 in one call, 1 credit per contract) or `defrr_create_contract` for a single contract. These only calculate: nothing is stored.

Anything I have given you after the command is what I have so far. If I have given you nothing, ask me which contracts to include.

Steps:

1. Get the contracts. Either I list them (customer, each term's start date, end date and price, the original term first and then any renewals), or I name customers that are already in defrr: find each with `defrr_find_contracts`, read it with `defrr_get_contract` and use the `schedule` it returns exactly as it is. A contract that returns no schedule (for example it bills monthly, has a discount, or carries adjustments) can't be included: list those by name at the end and say why.
2. For contracts I type in, ask whether revenue is recognised `exact` (per day) or `smooth` (equal months), and whether end dates are included (a 1 Jan to 31 Dec term means `endDateIncluded: true`). Do not guess dates or prices. If you are connected to an organization, use its default recognition and tell me which you used.
3. Before you run it, tell me how many contracts that is and what it will cost (1 credit each), and wait for me to confirm if it is more than a handful.
4. Run it. If there are more than 50 contracts, send them in batches of up to 50, saying which batch each is and passing in the carry the previous batch returned. The last batch gives the grand total.
5. Show the result: first the total across the contracts by month, then each contract's own schedule, and mention the balance check (billed and recognised both equal the contracts' prices, and the deferred balance ends at zero). If I want it for a spreadsheet, ask for the `rows` shape. For hundreds of contracts, suggest calling the REST API from a script instead of sending them through the model.
