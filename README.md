# mcp-sportsdataio

SportsDataIO MCP — wraps SportsDataIO's Big-Six sports data API (sportsdata.io)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1866+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

## Tools

| Tool | Description |
|------|-------------|
| `sportsdataio_injuries` | Current injuries in the NFL / NBA / MLB / NHL / CFB / CBB — returns each injured player with team, position, status (Out, Questionable, etc.) and the body part affected. Note: a free SportsDataIO trial key returns scrambled sample data, not real injuries. Example: sportsdataio_injuries({ league: "nfl", _apiKey: "your-key" }) |
| `sportsdataio_projections` | Projected player stats for a slate — fantasy points plus key projected stats per player. Daily sports (NBA / MLB / NHL / CBB) take a `date`; weekly sports (NFL / CFB) take `season` + `week`. Note: a free trial key returns scrambled sample data. Example: sportsdataio_projections({ league: "nba", date: "2026-01-15", _apiKey: "your-key" }) or sportsdataio_projections({ league: "nfl", season: "2025REG", week: 5, _apiKey: "your-key" }) |
| `sportsdataio_player_props` | Player prop bets for a date — available betting markets and outcomes (over/under totals, payouts) across NFL / NBA / MLB / NHL / CFB / CBB. Requires a SEPARATE SportsDataIO Odds/Props API key (the Props warehouse is billed apart from Stats/Projections). Note: a free trial key returns scrambled sample data. Example: sportsdataio_player_props({ league: "nba", date: "2026-01-15", _apiKey: "your-odds-key" }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "sportsdataio": {
      "url": "https://gateway.pipeworx.io/sportsdataio/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/sportsdataio/mcp` returns the tools in the table
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

Both URLs reach the same gateway and the same 1866+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

This pack takes your own API key (`_apiKey`) — we don't front one for it, so there's no curl here that would run without it. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/sportsdataio_injuries`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-sportsdataio` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/sportsdataio/mcp` endpoint shown above. It needs network access;
there is no offline mode.

```json
{
  "mcpServers": {
    "sportsdataio": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-sportsdataio"]
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
ask_pipeworx({ question: "your question about Sportsdataio data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
