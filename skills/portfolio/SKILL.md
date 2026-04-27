---
name: portfolio
description: Show a consolidated cross-chain portfolio overview with USD valuations for all token holdings and DeFi positions. Use when the user asks about their portfolio, net worth, total value, or asset breakdown across chains.
license: MIT
compatibility: Requires Node.js 18+ and npx. Uses `npx fibx@latest`.
metadata:
    version: 0.7.0
    author: ahmetenesdur
    category: wallet-data
allowed-tools:
    - Bash(npx fibx@latest portfolio *)
    - Bash(npx fibx@latest portfolio)
    - Bash(npx fibx@latest status)
---

# Portfolio Overview

Fetch a consolidated view of all token holdings with USD valuations across **all supported chains** (Base, Citrea, HyperEVM, Monad) and DeFi positions (Aave V3 on Base).

## Prerequisites

- Active session required. If not authenticated, run `authenticate-wallet` skill first.

## Rules

1. This command fetches **all chains in parallel** — no `--chain` flag needed.
2. Use `--json` when the output will be consumed by another skill or pipeline.
3. Token prices are sourced live from Fibrous. Values are approximate and reflect DEX prices, not CEX quotes.
4. Chains with zero holdings are automatically hidden.
5. DeFi positions (Aave V3) are included if the user has any collateral or debt on Base.

## Commands

```bash
npx fibx@latest portfolio [--json]
```

## Parameters

| Parameter | Type | Description    | Required |
| --------- | ---- | -------------- | -------- |
| `json`    | flag | Output as JSON | No       |

## Output

### Table Mode (default)

Displays chain-grouped token holdings with USD values, Aave V3 DeFi positions (if any), and a total portfolio value.

### JSON Mode (`--json`)

```json
{
	"wallet": "0x...",
	"chains": [
		{
			"chain": "base",
			"totalUsd": 205.48,
			"assets": [
				{ "symbol": "ETH", "balance": "0.0042", "price": 2350.0, "usdValue": 9.87 },
				{ "symbol": "USDC", "balance": "125.50", "price": 1.0, "usdValue": 125.5 }
			]
		}
	],
	"defi": [
		{
			"protocol": "Aave V3",
			"chain": "base",
			"collateralUsd": 500.0,
			"debtUsd": 200.0,
			"healthFactor": "2.50",
			"netUsd": 300.0
		}
	],
	"totalUsd": 517.98
}
```

## Examples

**User:** "Show me my portfolio"

```bash
npx fibx@latest portfolio
```

**User:** "What's my total net worth?"

```bash
npx fibx@latest portfolio
```

**User:** "Get my portfolio as JSON"

```bash
npx fibx@latest portfolio --json
```

## Error Handling

| Error               | Action                                                     |
| ------------------- | ---------------------------------------------------------- |
| `Not authenticated` | Run `authenticate-wallet` skill first.                     |
| `Network error`     | Retry once. If persistent, use `config` to set custom RPC. |
| `Rate limit / 429`  | Use `config` skill to set a custom RPC.                    |

## Related Skills

- Use `balance` for detailed single-chain balances without USD values.
- Use `aave` to manage individual DeFi positions shown in the portfolio.
- Use `trade` to rebalance assets across tokens.
- Use `config` to set a custom RPC if you encounter rate limit errors.
