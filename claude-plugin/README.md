# defrr for Claude Code

This plugin connects Claude Code to [defrr](https://defrr.com/developers), which works out revenue recognition for contracts: month-by-month schedules, renewals, price corrections, cancellations and usage pricing, in your base currency or a foreign one, including back-dated contracts. Claude understands what you ask for, and defrr's engine does the arithmetic the same way every time, so the schedule's billing, revenue and deferred balance always add up.

## Install

```
claude plugin marketplace add defrr-com/defrr-mcp
claude plugin install defrr@defrr
```

Then, in Claude Code, run `/plugin configure defrr@defrr` and paste your defrr API key. Claude Code keeps it in secure storage. Create a key at https://defrr.com/developers/account (40 free credits). A key that only calculates stores nothing in defrr. Tick **Save contracts in Defrr** on the key if you want Claude to save contracts and renewals as drafts for a person to approve.

## What you get

- The defrr tools: create a contract, build a waterfall, process a renewal, correct a price, cancel a contract or renewal, price usage, and (with a saving key) save drafts, read a customer's contracts and set up usage products.
- A skill that tells Claude when to use them and how to ask for missing dates and prices.
- Guided commands that ask for anything missing, read the terms back and then act:
  - `/defrr:new-contract` saves a new contract as a draft.
  - `/defrr:renewal` finds a customer's contract and saves a renewal as a draft.
  - `/defrr:price-correction` works out the credit when a contract or renewal was priced too high (calculates only).
  - `/defrr:cancellation` shows what cancelling a contract or its latest renewal would do (calculates only).
  - `/defrr:recognition-schedules` shows billing, revenue and the deferred balance month by month for one or more contracts, with a total.

## Data handling

The calculation tools work only from what you send and store nothing. Saving is off unless the key allows it, and saved contracts are drafts that stay out of every report until a person approves them in defrr. The plugin runs no code on your machine and sends your key only to https://defrr.com/v1/mcp. Privacy policy: https://defrr.com/privacy. Support: https://defrr.com/contact.
