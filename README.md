# defrr MCP server

Revenue schedules, credits and cancellations for contracts, worked out by defrr's engine.
Connect it to Claude Code and ask in plain English.
Nothing you send is stored or logged.

## Connect

1. Create an API key at https://defrr.com/developers/account (25 free credits).
2. Add the server:

   claude mcp add --transport http defrr https://defrr.com/v1/mcp \
     --header "Authorization: Bearer dfr_live_YOUR_KEY"

3. In Claude Code, type /mcp. defrr should show 6 tools.

## Tools

| Tool | What it does | Credits |
|---|---|---|
| defrr_create_contract | New contract: terms in, month-by-month schedule out | 1 |
| defrr_reprice_renewal | Credit for a renewal priced too high | 2 |
| defrr_reprice_contract | Credit for an original term priced too high | 2 |
| defrr_cancel_renewal | Cancel the latest renewal | 2 |
| defrr_cancel_contract | Cancel a whole contract | 2 |
| defrr_get_credit_balance | Check your credits | Free |

## Try it

> Use defrr to create a contract: original term 1 Jan 2026 to 31 Dec 2026 at $12,000,
> then a renewal from 1 Jan 2027 to 31 Dec 2027 at $18,000. End dates are included.
> Recognise it smooth. Show me the terms and the month-by-month schedule.

## Docs and limits

Full reference, pricing and what isn't covered: https://defrr.com/developers/guide

Registry listing: `com.defrr/defrr`
