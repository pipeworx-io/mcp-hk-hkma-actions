# @pipeworx/hk-hkma-actions

Hong Kong Monetary Authority (HKMA) enforcement releases against firms: disciplinary actions and pecuniary penalties on banks for Anti-Money Laundering and Counter-Terrorist Financing Ordinance breaches, on stored value facility licensees (e-wallets) under the Payment Systems and Stored Value Facilities Ordinance, and the HKMA's joint releases with the Securities and Futures Commission that name a bank.

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 2007+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

## Tools

- `hk_hkma_actions_search(query?, year?, limit?)` -- searches the HKMA enforcement releases, newest first. The keyword matches the release title and the firm name. Each hit: release id, date, firm, kind of action, link. `individuals_dropped` counts the releases about individuals that were not returned. A search with no hit says which dates were read.
- `hk_hkma_action_get(id)` -- one release by its id (such as `2025/07/20250722-4`): date, firm or banks, the action taken, penalty amounts in HK$, the findings, and links to the Statements of Disciplinary Action. An unknown id is `found:false`; a release about an individual is `found:false` with `empty_reason: not_a_firm_action`.

Every response carries `source` and `data_as_of` (the newest firm release date read).

## Auth

Keyless.

## Data sources

- Hong Kong Monetary Authority, enforcement press releases. Read live per call, nothing stored.

Coverage: the list read is the HKMA's enforcement press-release list, which shows the ten most recent releases; a search covers the dates in `searched.earliest_date` to `searched.latest_date`. Older releases are on the HKMA site and are not reached by this pack.

Privacy: firms only. Releases whose subject is an individual (bank staff, insurance intermediaries) are dropped and counted, and quoted remarks by named HKMA officials are removed from release text.

Traps worth knowing: a zero-match search means no match in that window, not that a bank has no enforcement history; a joint release names the firm the other regulator acted against (the HKMA's own action is on the SFC or Insurance Authority side).

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "hk-hkma-actions": {
      "url": "https://gateway.pipeworx.io/hk-hkma-actions/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/hk-hkma-actions/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 2007+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/hk_hkma_actions_search \
  -H 'Content-Type: application/json' \
  -d '{"query":"bank"}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/hk_hkma_actions_search`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-hk-hkma-actions` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/hk-hkma-actions/mcp` endpoint shown above. It needs network access;
there is no offline mode.

```json
{
  "mcpServers": {
    "hk-hkma-actions": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-hk-hkma-actions"]
    }
  }
}
```

Without a key it calls anonymously, capped at 50 calls a day. A free account
raises that to 200 a day: get a key at https://pipeworx.io/signup and add
`"env": { "PIPEWORX_API_KEY": "<your key>" }` to the server entry.
The key is sent as a Bearer token, the same way a hosted connection sends it,
so calls are metered and billed exactly as they are there.

It lists **only** this pack's tools by default. Set `PIPEWORX_META_TOOLS=1` to
also list `ask_pipeworx` and the other gateway-wide tools.

Behind an HTTPS proxy: Node's built-in `fetch` does not read `HTTPS_PROXY` by
default. Newer Node releases honour it when `NODE_USE_ENV_PROXY=1` is set; check
your Node version's documentation.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Hk Hkma Actions data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
