# nt-mcp-server — MCP server for NinjaTrader 8

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

Connect AI agents (Claude, Hermes, ChatGPT, Cursor, Cline) to **NinjaTrader 8** via the [Model Context Protocol (MCP)](https://modelcontextprotocol.io/).

Your AI can read positions, check balances, place and cancel orders, fetch real-time quotes, pull historical bars, search instruments, and run NinjaScript strategies — through a single stdio interface.

## Architecture

```
AI Client (MCP stdio)  →  nt-mcp-server.js  →  HTTP :7890  →  NT8 McpBridgeAddOn
```

Three layers, zero external APIs, everything runs locally on your machine.

## Quick Start

### 1. Install the NT8 AddOn

1. Open **NinjaTrader 8**
2. `New` → `NinjaScript Editor` (F11)
3. Right-click `AddOns` in the left panel → `New AddOn...`
4. Replace the file contents with `nt8-addon/McpBridgeAddOn.cs`
5. Press **F5** to compile
6. Restart NinjaTrader

Alternatively, copy `nt8-addon/McpBridgeAddOn.cs` to:
```
Documents\NinjaTrader 8\bin\Custom\AddOns\
```
and compile via NinjaScript Editor (F5).

Verify the AddOn is running:
```powershell
curl http://localhost:7890/api/health
# {"status":"ok","timestamp":"...","version":"0.1.0"}
```

### 2. Start the MCP Server

```bash
node nt-mcp-server.js
```

Expected output:
```
[nt-mcp] Server started — NT8 at http://127.0.0.1:7890
[nt-mcp] Waiting for MCP messages on stdin...
```

### 3. Configure Your AI Client

**Claude Desktop** (`claude_desktop_config.json`):
```json
{
  "mcpServers": {
    "ninjatrader": {
      "command": "node",
      "args": ["C:/path/to/nt-mcp-server.js"]
    }
  }
}
```

**Hermes Agent** (`~/.hermes/config.yaml`):
```yaml
mcpServers:
  ninjatrader:
    command: node
    args: ['C:\path\to\nt-mcp-server.js']
    transport: stdio
```

## Tools (Phase 1)

| Tool | Description |
|------|-------------|
| `nt_health` | Check connection to NinjaTrader 8 |
| `nt_accounts` | List accounts, balances, buying power |
| `nt_positions` | List open positions with PnL |
| `nt_orders` | List working orders with status |
| `nt_place_order` | Place Market / Limit / StopMarket / StopLimit orders |
| `nt_cancel_order` | Cancel an order by ID or name |
| `nt_cancel_all_orders` | Cancel all working orders across all accounts |
| `nt_quote` | Real-time quote (bid, ask, last, volume, daily high/low) |
| `nt_bars` | Historical OHLCV bars (Minute, Day, Tick, Volume, Range) |
| `nt_search` | Search instruments by name or symbol |
| `nt_execute_strategy` | Launch a NinjaScript strategy on a chart |

## Configuration

Environment variables:

| Variable | Default | Description |
|----------|---------|-------------|
| `NT8_HOST` | `127.0.0.1` | NT8 AddOn hostname |
| `NT8_PORT` | `7890` | NT8 AddOn HTTP port |

## Roadmap (Phase 2)

- `nt_backtest` — run Strategy Analyzer, return full trade list + performance metrics
- `nt_compile` — compile NinjaScript source in memory (incremental, no disk write)
- `nt_chart_state` — read chart state (symbol, timeframe, indicators)
- `nt_indicator_values` — pull live indicator values from open charts
- `nt_deploy_strategy` — deploy, verify, and monitor a strategy in production
- Full backtest → optimize → deploy → monitor pipeline

## Requirements

- **Node.js 18+** (uses only built-in modules — zero npm dependencies)
- **NinjaTrader 8** (any license: free, trial, or lifetime)
- **Windows** (NinjaTrader only runs on Windows)

## License

MIT — do what you want, no strings attached. See [LICENSE](LICENSE).

## Author

Built by [Igor](https://github.com/Wendigooor) and his AI agent Hermes.
