# defrr MCP server

Revenue recognition (RevRec) schedules, waterfalls, renewals, credits, cancellations and usage pricing for contracts, worked out by defrr's engine.
Connect it to Claude Code and ask in plain English, or call it from your own code.

The calculation tools store nothing, and nothing you send to them is logged. If you want, you can also switch on **saving contracts to your defrr organization** (see below). That is off by default and set per key.

## Connect

1. Create an API key at https://defrr.com/developers/account (40 free credits).
2. Add the server:

   ```
   claude mcp add --transport http defrr https://defrr.com/v1/mcp \
     --header "Authorization: Bearer dfr_live_YOUR_KEY"
   ```

3. In Claude Code, type `/mcp`. defrr should show 9 tools for a key that only calculates, or 12 or 14 for a key that can also save contracts (see below).

## Tools

These calculate and store nothing. They work for every key.

| Tool | What it does | Credits |
|---|---|---|
| `defrr_create_contract` | New contract: terms in, month-by-month schedule out. Saves nothing | 1 |
| `defrr_create_waterfall` | Up to 50 contracts at once: each contract's billing, revenue and deferred balance by month, and the total | 1 per contract |
| `defrr_process_renewal` | Renewals: the price change, the billing entry, the revised schedule, and what to post by month. A renewal processed after it started has its catch-up posted in the first open month | 1 per renewal |
| `defrr_usage_billing` | What usage costs under flat, volume or prepaid-credit pricing, month by month, with any prepaid overage flagged | 1 per line |
| `defrr_reprice_renewal` | Credit for a renewal priced too high | 2 |
| `defrr_reprice_contract` | Credit for an original term priced too high | 2 |
| `defrr_cancel_renewal` | Cancel the latest renewal | 2 |
| `defrr_cancel_contract` | Cancel a whole contract | 2 |
| `defrr_get_credit_balance` | Check your credits | Free |

A call that fails is refunded.

## Saving contracts to defrr (optional)

By default the tools above only calculate: the schedule comes back to you and nothing is kept. If you want contracts recorded in your defrr organization, create a key with **Save contracts in Defrr** ticked on your [developer account](https://defrr.com/developers/account). If you don't have a defrr workspace yet, the page sets up a small one for you (company name, base currency, how revenue is recognised and, if you like, the month your books are locked through). A key without that option can only calculate.

| Tool | What it does | Needs | Credits |
|---|---|---|---|
| `defrr_save_contract` | Saves a contract as a **draft** and works out its schedule | the save option | 1 (a repeat is free) |
| `defrr_list_drafts` | Lists the drafts waiting for approval, or already decided | the save option | Free |
| `defrr_get_workspace` | Shows the organization, its base currency and recognition model, its locked period, how many drafts are waiting, and when the key last connected | the save option | Free |
| `defrr_approve_draft` | Makes a draft a live contract | the approve option | Free |
| `defrr_reject_draft` | Discards a draft (it is kept as rejected) | the approve option | Free |

**A saved contract is a draft.** It isn't live, and no report reads it, until it is approved: in defrr under Contracts, Draft contracts, or by an agent whose key also has the approve option. Leave the approve option off for an agent you want a person to check. If a draft has revenue in months you have closed (your locked period in Organization details), or in months that have already ended, defrr says so before it is approved.

**Repeats are safe.** Sending the same agreement again returns the draft already saved and costs nothing, while that draft is waiting or approved. If it was rejected, or its contract has since been deleted, the same terms save as a new draft. Send an `idempotencyKey` to save a separate deal with identical terms.

**What the agent is told.** When a session starts it learns which organization it is connected to, the last locked period, and when the key last connected. Only a person can change the locked period, in defrr under Organization details.

### Be stepped through it

In Claude Code, start a new session and type:

```
/mcp__defrr__new-contract
```

It asks for anything missing (customer, product, each term's dates and price, how revenue is recognised), reads the terms back for you to confirm, then saves a draft. You can put what you already know after the command. Other MCP clients list the same prompt as `new-contract` on the defrr server, in their own menu. Or just ask in plain English: "Create a contract for Acme Pty Ltd, Pro Plan, 1 Jan 2027 to 31 Dec 2027 at $12,000".

### A developer workspace

A workspace set up from the developer account page opens a few pages in the defrr app: the Dashboard, View all contracts, Draft contracts, the customer profile, and Organization details (the basic details, recognition model, base currency and locked period). The rest are subscription features and are shown greyed out.

## Try it

A new contract (calculate only):

> Use defrr to create a contract: original term 1 Jan 2026 to 31 Dec 2026 at $12,000,
> then a renewal from 1 Jan 2027 to 31 Dec 2027 at $18,000. End dates are included.
> Recognise it smooth. Show me the terms and the month-by-month schedule.

A renewal:

> Use defrr to process a renewal, recognised smooth with end dates included. The contract ran
> 1 Jan 2026 to 31 Dec 2026 at $12,000 and renews to 31 Dec 2027 at $18,000. Our books are
> closed through March 2027. Show the price change, the billing entry, any catch-up, and what
> to post by month.

Usage:

> Use defrr to price usage for two customers. Acme is on volume pricing: the first 100,000 units
> a month at $0.002, then $0.001 a unit above that. They used 250,000 units in Jan 2026 and
> 80,000 in Feb 2026. Globex has prepaid credit: 100,000 units at $0.002 each. They used 70,000
> units in Jan 2026 and 60,000 in Feb 2026. Show each month's revenue, the total by month, and
> any overage.

Saving a contract (needs a key with the save option):

> Create a contract in defrr for Acme Pty Ltd, Pro Plan: 1 Jan 2027 to 31 Dec 2027 at $12,000,
> exact recognition, end date included. Tell me which organization it was saved to.

## Other clients

Any MCP client that supports Streamable HTTP and custom headers can connect. Send your key
either as `Authorization: Bearer dfr_live_YOUR_KEY` or as `X-API-Key: dfr_live_YOUR_KEY` (for
tools that don't let you set `Authorization`). Never put a key in the web address.

### LangChain

LangChain connects to MCP servers through its
[`langchain-mcp-adapters`](https://github.com/langchain-ai/langchain-mcp-adapters) package:

```python
from langchain_mcp_adapters.client import MultiServerMCPClient

client = MultiServerMCPClient({
    "defrr": {
        "transport": "streamable_http",
        "url": "https://defrr.com/v1/mcp",
        "headers": {"X-API-Key": "dfr_live_YOUR_KEY"},
    }
})
tools = await client.get_tools()
```

Keep your key in an environment variable rather than in the code. See LangChain's
[MCP documentation](https://docs.langchain.com/oss/python/langchain/mcp) for how to use the
tools in an agent.

## Larger sets

A revenue waterfall takes up to 50 contracts per call. For more, send them in batches and pass
each response's `carry` into the next call: the last batch returns the grand total for all of
them. For a big set (hundreds or thousands of contracts), call the API from a script instead of
through the model, so the figures go straight into a file. The guide has a ready-made Python
script that writes two CSV files.

## Privacy

The calculation tools work from what you send in each request and keep nothing about it. The one exception is saving contracts, which you switch on per key: a key with **Save contracts in Defrr** stores the contract you send (customer, product, dates and prices) as a draft in your own defrr organization.

## Docs and limits

Full reference, pricing and what isn't covered: https://defrr.com/developers/guide

Registry listing: `com.defrr/defrr`
