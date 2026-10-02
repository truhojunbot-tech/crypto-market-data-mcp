## What does Crypto Data MCP Server do?

**Crypto Data MCP Server** is a persistent **crypto data MCP server** built on Apify Standby, designed to feed live market intelligence directly into AI agents via the **Model Context Protocol**. Instead of one-shot scraping, it runs as a long-lived HTTP server that your AI assistant queries on demand — delivering real-time kimchi premium, DeFi yields, ETF flows, Fear & Greed index, funding rates, and market overview **without any API keys or setup**. As a native **Model Context Protocol crypto** integration on Apify, it stays warm and responds in milliseconds, making it the easiest path to plug live crypto context into Claude Desktop, GPT, or any MCP-compatible agent.

> **Important:** This is a **server actor**, not a one-shot scraper. Do NOT click "Run" — use the **Standby URL** to connect your AI agent. Clicking "Run" starts a short-lived instance that will time out.

---

## Why use Crypto Data MCP Server?

**AI crypto data integration** has never been simpler — no credential management, no infrastructure, no polling loops.

- **AI trading agents** — inject live funding rates, ETF flows, and Fear & Greed scores into automated strategy signals without managing API keys yourself
- **Claude Desktop / ChatGPT plugins** — ask "what's BTC fear & greed today?" and get a live answer in chat, powered by this MCP server
- **Crypto research pipelines** — pull structured market data on demand into notebooks, dashboards, or LLM chains
- **Crypto chatbots** — add live DeFi yield data and kimchi premium to any LLM application without backend work
- **Quantitative analysts** — fetch funding rate spreads across CEXs and ETF flow signals as clean JSON, ready for downstream processing

---

## How to use Crypto Data MCP Server

1. Open the actor page and click the **Integrations** tab
2. Copy your personal **Standby URL** (format: `https://xxxxxxxxxxxxxxxx.apify.actor`)
3. Add the server to your Claude Desktop config (`claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "crypto-data": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://YOUR_STANDBY_URL/mcp/v1"]
    }
  }
}
```

4. Restart Claude Desktop — the 6 crypto tools appear automatically in the tools panel
5. Ask Claude anything: "What are the top DeFi yields above 10% APY?" — it calls the MCP server live

---

## Input

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `port` | integer | `8080` | Internal port for the MCP server (no change needed for most users) |

---

## Output

The MCP server returns structured JSON per tool call. Example responses:

```json
{
  "tool": "get_fear_greed",
  "value": 62,
  "classification": "Greed",
  "timestamp": "2026-09-02T09:00:00Z"
}
```

```json
{
  "tool": "get_etf_flows",
  "etf_type": "us-btc-spot",
  "date": "2026-09-01",
  "net_flow_usd": 312000000,
  "inflow_usd": 480000000,
  "outflow_usd": 168000000
}
```

---

## Data fields

| Tool | Key Fields |
|------|------------|
| `get_kimchi_premium` | `premium_pct`, `btc_krw_price`, `btc_global_price` |
| `get_defi_yields` | `protocol`, `pool`, `apy`, `tvl_usd`, `chain` |
| `get_etf_flows` | `etf_type`, `net_flow_usd`, `inflow_usd`, `outflow_usd`, `date` |
| `get_fear_greed` | `value`, `classification`, `timestamp` |
| `get_funding_rates` | `symbol`, `exchange`, `funding_rate_8h`, `funding_rate_apr` |
| `get_market_overview` | `btc_dominance_pct`, `total_market_cap_usd`, `top_movers` |

---

## Available Tools

| Tool | Description |
|------|-------------|
| `get_kimchi_premium` | Korean exchange premium vs global BTC/ETH price |
| `get_defi_yields` | Top DeFi yield opportunities (DefiLlama), filterable by APY and TVL |
| `get_etf_flows` | BTC/ETH spot ETF daily inflow/outflow data |
| `get_fear_greed` | Crypto Fear & Greed Index (0–100) |
| `get_funding_rates` | Perpetual funding rates across major CEXs |
| `get_market_overview` | BTC dominance, total market cap, top movers |

---

## MCP Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/mcp/v1` | POST | MCP tool call (JSON-RPC 2.0) |
| `/mcp/v1/tools` | GET | List available tools |
| `/health` | GET | Health check |

---

## Tool Parameters

### `get_defi_yields`
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `min_apy` | number | 5.0 | Minimum APY % filter |
| `min_tvl` | number | 1,000,000 | Minimum TVL in USD |
| `limit` | integer | 20 | Max results returned |

### `get_etf_flows`
| Parameter | Type | Values |
|-----------|------|--------|
| `etf_type` | string | `us-btc-spot`, `us-eth-spot` |

### `get_funding_rates`
| Parameter | Type | Default |
|-----------|------|---------|
| `symbols` | array | `["BTC", "ETH", "SOL", "BNB", "XRP"]` |

---

## Cost estimation

This actor runs in **Standby mode** — you pay only for compute time while the server is active.

| Usage Pattern | Estimated Cost |
|---------------|----------------|
| Occasional queries (fewer than 10/day) | ~$0.01–0.05/day |
| Active AI agent (50–200 queries/day) | ~$0.10–0.30/day |
| Heavy pipeline use | ~$0.50+/day |

Standby actors bill per second of active compute. Idle time is not charged.

---

## FAQ

**Do I need an Apify API token to use the MCP tools?**
No. Once the actor is running via its Standby URL, your AI agent connects directly to the MCP endpoint. No Apify credentials are passed in tool calls.

**What data sources does it use?**
CoinGecko (market overview), DefiLlama (DeFi yields), SoSoValue (ETF flows), Alternative.me (Fear & Greed), and public exchange APIs (funding rates). No paid API keys required.

**Can I use this with ChatGPT or other LLMs?**
Yes. Any MCP-compatible client can connect. For non-MCP setups, you can also call the REST endpoints directly via the Standby URL.

> **Disclaimer:** Data is sourced from public APIs and provided for informational purposes only. Nothing in this actor constitutes financial advice. Always verify data before making trading decisions.

---

## Related actors

- [Kimchi Premium Tracker](https://apify.com/gochujang/kimchi-premium-tracker) — Korea exchange premium data served by this MCP server
- [Fear & Greed Tracker](https://apify.com/gochujang/fear-greed-tracker) — Fear & Greed index data accessible through this MCP integration
- [DeFi Yield APY Scanner](https://apify.com/gochujang/defi-yield-apy-scanner) — DeFi yield data that this MCP server exposes to AI agents

## Feedback

If this actor powers your AI agent setup, a review helps others find it: [Leave a review on Apify Store](https://apify.com/gochujang/crypto-data-mcp#reviews)

---

**Keywords:** crypto data MCP server, AI agent crypto tools, kimchi premium API, DeFi yield API, Fear Greed Index API, crypto funding rates API, real-time crypto market data, MCP crypto tools, Claude AI crypto data, LLM crypto data server, Apify standby server crypto, crypto REST API
