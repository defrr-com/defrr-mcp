# defrr MCP server

Revenue recognition (RevRec) schedules, waterfalls, renewals, credits, cancellations and usage pricing for contracts, worked out by defrr's engine.
Connect it to Claude Code and ask in plain English, or call it from your own code.

The calculation tools store nothing, and nothing you send to them is logged. If you want, you can also switch on **saving to your defrr organization** (see below): contracts and renewals are saved as drafts a person approves, and usage products can be set up. That is off by default and set per key.

## Connect

1. Create an API key at https://defrr.com/developers/account (40 free credits).
2. Add the server:

   ```
   claude mcp add --transport http defrr https://defrr.com/v1/mcp \
     --header "Authorization: Bearer dfr_live_YOUR_KEY"
   ```

3. In Claude Code, type `/mcp`. defrr should show 9 tools for a key that only calculates, or 18 or 23 for a key that can also save contracts (see below).

## Tools

These calculate and store nothing. They work for every key.

| Tool | What it does | Credits |
|---|---|---|
| `defrr_create_contract` | New contract: terms in, month-by-month schedule out. Saves nothing | 1 |
| `defrr_create_waterfall` | Up to 50 contracts at once: each contract's billing, revenue and deferred balance by month, and the total | 1 per contract |
| `defrr_process_renewal` | Renewals: the price change, the billing entry, the revised schedule, and what to post by month. A renewal processed after it started has its catch-up posted in the first open month | 1 per renewal |
| `defrr_usage_billing` | What usage costs under flat, volume, prepaid-credit or fixed-fee-plus-overage pricing, month by month. Prepaid overage is flagged; a fixed fee's overage is priced and returned apart from revenue. Give a fixed-fee line a `startDate` and dated usage and its cycles follow the contract's anniversary: the fee is booked in the month each cycle starts and the overage in the month it ends. Stateless: your usage is calculated and returned, never stored | 1 per line |
| `defrr_reprice_renewal` | Credit for a renewal priced too high | 2 |
| `defrr_reprice_contract` | Credit for an original term priced too high | 2 |
| `defrr_cancel_renewal` | Cancel the latest renewal | 2 |
| `defrr_cancel_contract` | Cancel a whole contract | 2 |
| `defrr_get_credit_balance` | Check your credits | Free |

A call that fails is refunded.

## Saving to defrr (optional)

By default the tools above only calculate: the schedule comes back to you and nothing is kept. If you want contracts and renewals recorded in your defrr organization, create a key with **Save contracts in Defrr** ticked on your [developer account](https://defrr.com/developers/account). If you don't have a defrr workspace yet, the page sets up a small one for you (company name, base currency, how revenue is recognised and, if you like, the month your books are locked through). A key without that option can only calculate.

| Tool | What it does | Needs | Credits |
|---|---|---|---|
| `defrr_save_contract` | Saves a contract as a **draft** and works out its schedule | the save option | 1 (a repeat is free) |
| `defrr_list_drafts` | Lists the drafts waiting for approval, or already decided | the save option | Free |
| `defrr_get_workspace` | Shows the organization, its base currency and recognition model, its locked period, how many drafts are waiting, and when the key last connected | the save option | Free |
| `defrr_approve_draft` | Makes a draft a live contract | the approve option | Free |
| `defrr_reject_draft` | Discards a draft (it is kept as rejected) | the approve option | Free |
| `defrr_save_renewal` | Saves a renewal for a contract already in the workspace as a draft: the contract is not changed until the renewal is approved. Works for contracts whose terms can be read as a schedule, and refuses a renewal that starts in a closed period | a save or approve key | 1 (a repeat is free) |
| `defrr_list_renewal_drafts` | Lists renewal drafts waiting for approval, or already decided | a save or approve key | Free |
| `defrr_approve_renewal_draft` | Adds a draft renewal to its contract as a new last term and recalculates its schedules | the approve option | Free |
| `defrr_reject_renewal_draft` | Discards a draft renewal (it is kept as rejected) | the approve option | Free |
| `defrr_find_contracts` | Finds a customer's contracts in the workspace by name | a save or approve key | Free |
| `defrr_get_contract` | Reads one contract and returns its terms as a schedule the calculation tools take. Says what is in the way when a contract has features the calculators don't model | a save or approve key | Free |
| `defrr_list_usage_products` | Lists the usage products already set up in the workspace | a save or approve key | Free |
| `defrr_preview_usage_product` | Shows what sample customers would pay under a usage product (pay as you go, prepaid credits or fixed fee plus overage) before it exists. Saves nothing | a save or approve key | Free |
| `defrr_create_usage_product` | Creates that usage product in the workspace (a new product only; existing ones are edited in defrr) | the approve option | Free |

**A saved contract is a draft.** It isn't live, and no report reads it, until it is approved: in defrr under Contracts, Draft contracts, or by an agent whose key also has the approve option. Leave the approve option off for an agent you want a person to check. If a draft has revenue in months you have closed (your locked period in Organization details), or in months that have already ended, defrr says so before it is approved.

**Repeats are safe.** Sending the same agreement again returns the draft already saved and costs nothing, while that draft is waiting or approved. If it was rejected, or its contract has since been deleted, the same terms save as a new draft. Send an `idempotencyKey` to save a separate deal with identical terms.

**A saved renewal is a draft too.** When a customer renews, the agent finds the contract (`defrr_find_contracts`, then `defrr_get_contract`) and saves the renewal with `defrr_save_renewal`: the contract's id, the renewal's end date and the renewal term's total price. The contract does not change until the renewal is approved in defrr under Contracts, Draft renewals, or with `defrr_approve_renewal_draft` if the key may approve. Approving adds the renewal as a new last term and recalculates the contract's schedules. It only works on a contract whose terms can be read as a schedule (`defrr_get_contract` says why when it can't, for example monthly billing, a discount or a foreign currency), only after the contract's last end date, and it refuses a renewal that starts in a closed period: process that one in the app under Edit renewals.

**What the agent is told.** When a session starts it learns which organization it is connected to, the last locked period, and when the key last connected. Only a person can change the locked period, in defrr under Organization details.

### Be stepped through it

In Claude Code, start a new session and type:

```
/mcp__defrr__new-contract
```

It asks for anything missing (customer, product, each term's dates and price, how revenue is recognised), reads the terms back for you to confirm, then saves a draft. You can put what you already know after the command. Other MCP clients list the same prompt as `new-contract` on the defrr server, in their own menu. Or just ask in plain English: "Create a contract for Acme Pty Ltd, Pro Plan, 1 Jan 2027 to 31 Dec 2027 at $12,000".

### A developer workspace

A workspace set up from the developer account page opens a few pages in the defrr app: the Dashboard, View all contracts, Draft contracts, Draft renewals, Usage products, the customer profile, and Organization details (the basic details, recognition model, base currency and locked period). The rest are subscription features and are shown greyed out.

It also has an **Ask defrr** assistant, a button at the bottom right of every page. It uses these same tools and your credits, and can also prepare the cancellation of a contract, or of its latest renewal: it shows the figures on a card and nothing changes until you press Confirm. It cannot approve drafts.

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

Saving a renewal (needs a key with the save option):

> Acme Pty Ltd renewed their Pro Plan to 31 Dec 2028 for $14,000. Find their contract and save the
> renewal as a draft. Tell me which contract you used.

Saving a contract (needs a key with the save option):

> Create a contract in defrr for Acme Pty Ltd, Pro Plan: 1 Jan 2027 to 31 Dec 2027 at $12,000,
> exact recognition, end date included. Tell me which organization it was saved to.

## Other clients

Any MCP client that supports Streamable HTTP and custom headers can connect. Send your key
either as `Authorization: Bearer dfr_live_YOUR_KEY` or as `X-API-Key: dfr_live_YOUR_KEY` (for
tools that don't let you set `Authorization`). Never put a key in the web address.

### Cursor

This repository is laid out as a [Cursor plugin](https://cursor.com/docs/plugins): the defrr connection (`mcp.json`, and the same file as `.mcp.json` for directories that look for that name), a rule that tells the agent when to use defrr (`rules/defrr-revops.mdc`), and a `new-contract` command that steps you through saving a contract (`commands/new-contract.md`).

The connection reads your key from an environment variable called `DEFRR_API_KEY`, so the key never goes into a file. Create a key at https://defrr.com/developers/account, then set the variable once:

- **macOS** (tested): in Terminal run

  ```
  launchctl setenv DEFRR_API_KEY dfr_live_YOUR_KEY
  ```

  Then quit Cursor completely (Cmd+Q) and open it again from the Dock or Applications folder, not by typing a command in Terminal. Cursor reads the variable when it starts, so reloading the window is not enough. The setting lasts until you restart your Mac: run the command again after a restart, or use the file-based setup below.
- **Windows and Linux** (not tested): set a user environment variable named `DEFRR_API_KEY`, then quit and reopen Cursor.

Then check Cursor's MCP settings: `defrr` should be ready and list its tools (9 for a key that only calculates, 18 or 23 for a key that can save contracts).

To use the plugin before it is in Cursor's marketplace, copy this folder to `~/.cursor/plugins/local/defrr/` and reload Cursor.

**If you would rather keep the key in a file**, or Cursor can't see the variable, it shows `defrr` as failed, often with `Unexpected token '<', "<!DOCTYPE"... is not valid JSON` in its MCP log. That message means the request reached defrr without a valid key. Add the server yourself in `~/.cursor/mcp.json` (or Cursor Settings, MCP):

```json
{
  "mcpServers": {
    "defrr": {
      "type": "http",
      "url": "https://defrr.com/v1/mcp",
      "headers": { "X-API-Key": "dfr_live_YOUR_KEY" }
    }
  }
}
```

Keep this file out of version control, and if you use the plugin as well, turn the plugin's own copy of the server off so the two don't clash.

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

The calculation tools work from what you send in each request and keep nothing about it. The one exception is saving, which you switch on per key: a key with **Save contracts in Defrr** stores the contracts and renewals you send (customer, product, dates and prices) as drafts in your own defrr organization, and the usage products you create there.

## Docs and limits

Full reference, pricing and what isn't covered: https://defrr.com/developers/guide

Registry listing: `com.defrr/defrr`
