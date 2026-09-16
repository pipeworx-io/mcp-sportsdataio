# mcp-sportsdataio

SportsDataIO MCP — wraps SportsDataIO's Big-Six sports data API (sportsdata.io)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1576+ live data sources.

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

Both URLs reach the same gateway and the same 1576+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

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

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-sportsdataio
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

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
