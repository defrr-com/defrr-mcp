# defrr MCP server

Revenue schedules, credits, cancellations and usage pricing for contracts, worked out by defrr's engine.
Connect it to Claude Code and ask in plain English.
Nothing you send is stored or logged.

## Connect

1. Create an API key at https://defrr.com/developers/account (25 free credits).
2. Add the server:

   ```
   claude mcp add --transport http defrr https://defrr.com/v1/mcp \
     --header "Authorization: Bearer dfr_live_YOUR_KEY"
   ```

3. In Claude Code, type `/mcp`. defrr should show 8 tools.

## Tools

| Tool | What it does | Credits |
|---|---|---|
| `defrr_create_contract` | New contract: terms in, month-by-month schedule out | 1 |
| `defrr_create_waterfall` | Up to 50 contracts at once: each contract's billing, revenue and deferred balance by month, and the total | 1 per contract |
| `defrr_reprice_renewal` | Credit for a renewal priced too high | 2 |
| `defrr_reprice_contract` | Credit for an original term priced too high | 2 |
| `defrr_cancel_renewal` | Cancel the latest renewal | 2 |
| `defrr_cancel_contract` | Cancel a whole contract | 2 |
| `defrr_usage_billing` | What usage costs under flat, volume or prepaid-credit pricing, month by month, with any prepaid overage flagged | 1 per line (up to 50) |
| `defrr_get_credit_balance` | Check your credits | Free |

## Try it

A new contract:

> Use defrr to create a contract: original term 1 Jan 2026 to 31 Dec 2026 at $12,000,
> then a renewal from 1 Jan 2027 to 31 Dec 2027 at $18,000. End dates are included.
> Recognise it smooth. Show me the terms and the month-by-month schedule.

Usage:

> Use defrr to price usage for two customers. Acme is on volume pricing: the first 100,000 units
> a month at $0.002, then $0.001 a unit above that. They used 250,000 units in Jan 2026 and
> 80,000 in Feb 2026. Globex has prepaid credit: 100,000 units at $0.002 each. They used 70,000
> units in Jan 2026 and 60,000 in Feb 2026. Show each month's revenue, the total by month, and
> any overage.

## Larger sets

A revenue waterfall takes up to 50 contracts per call. For more, send them in batches and pass
each response's `carry` into the next call: the last batch returns the grand total for all of
them. For a big set (hundreds or thousands of contracts), call the API from a script instead of
through the model, so the figures go straight into a file. The guide has a ready-made Python
script that writes two CSV files.

## Docs and limits

Full reference, pricing and what isn't covered: https://defrr.com/developers/guide

Registry listing: `com.defrr/defrr`
