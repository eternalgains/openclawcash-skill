```md
# OpenClawCash AgentWalletAPI Skill

`agentwalletapi` is a public skill for AI agents to operate OpenClawCash-managed wallets through the Agent API and MCP.

It supports:
- Wallet discovery and balances
- Transfers (EVM + Solana)
- Swaps and quotes (Uniswap/Jupiter)
- Approvals (EVM)
- Checkout escrow lifecycle
- Polymarket venue operations (orders, account, activity, positions, cancel)
- Polymarket market resolver (`marketUrl|slug + outcome -> tokenId`)

## What This Skill Is

- Canonical name: `agentwalletapi`
- Also referred to as: `openclawcash`
- Primary integration path: MCP (`@openclawcash/mcp-server`)
- Fallback path: CLI script (`scripts/agentwalletapi.sh`)

## Requirements

- `AGENTWALLETAPI_KEY` (required)
- `AGENTWALLETAPI_URL` (optional, default `https://openclawcash.com`)
- `curl` (required)
- `jq` (optional for pretty JSON)

## Quick Start

1. Set up env:
   ```bash
   bash scripts/setup.sh
   ```
2. Put your API key in `.env`:
   ```bash
   AGENTWALLETAPI_KEY=occ_your_api_key
   ```
3. Try read-only first:
   ```bash
   bash scripts/agentwalletapi.sh wallets
   ```

## MCP (Recommended)

Use the MCP server:
```bash
npx -y @openclawcash/mcp-server
```

Core Polymarket MCP tools:
- `polymarket_market_resolve`
- `polymarket_order_market`
- `polymarket_order_limit`
- `polymarket_account`
- `polymarket_orders`
- `polymarket_cancel_order`
- `polymarket_activity`
- `polymarket_positions`
- `polymarket_clear_integration`

## CLI Fallback Examples

```bash
# Wallets
bash scripts/agentwalletapi.sh wallets
bash scripts/agentwalletapi.sh wallet Q7X2K9P
bash scripts/agentwalletapi.sh balance Q7X2K9P

# Transfer
bash scripts/agentwalletapi.sh transfer Q7X2K9P 0xRecipient 100 USDC --yes

# Swap
bash scripts/agentwalletapi.sh quote mainnet WETH USDC 10000000000000000
bash scripts/agentwalletapi.sh swap Q7X2K9P WETH USDC 10000000000000000 0.5 --yes

# Polymarket resolve + order flow
bash scripts/agentwalletapi.sh polymarket-resolve https://polymarket.com/market/market-slug No
bash scripts/agentwalletapi.sh polymarket-market Q7X2K9P <tokenId> BUY 25 FAK 0.65 --yes
bash scripts/agentwalletapi.sh polymarket-orders Q7X2K9P OPEN 50
bash scripts/agentwalletapi.sh polymarket-cancel Q7X2K9P <orderId> --yes
```

## Polymarket Resolver

Agent endpoint:
```http
GET /api/agent/venues/polymarket/market/resolve?marketUrl=<url>&outcome=<label>
```
or
```http
GET /api/agent/venues/polymarket/market/resolve?slug=<slug>&outcome=<label>
```

Returns:
- market metadata
- normalized selected outcome
- resolved `tokenId`
- full outcome/token map

Use returned `outcome.tokenId` for market/limit order calls.

## Safety Model

- Start with read-only calls.
- Treat write calls as high-risk.
- Use explicit approval modes (`confirm_each_write` or `operate_on_my_behalf`).
- Use `--yes` only when write intent is explicit.
- Never commit secrets from `.env`.

## Notes

- All `/api/agent/*` calls require `X-Agent-Key`.
- Polymarket trading currently requires `polygon-mainnet` wallet + completed dashboard setup at:
  - `https://openclawcash.com/venues/polymarket`
- For close-position intent on open markets, default to market `SELL`.

## Links

- API docs: `https://openclawcash.com/docs`
- MCP page: `https://openclawcash.com/mcp`
- Changelog: `https://openclawcash.com/changelog`
- Skill raw: `https://openclawcash.com/agentwalletapi/SKILL.md`
```
