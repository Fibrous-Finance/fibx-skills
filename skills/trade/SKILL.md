---
name: trade
description: Swap or trade tokens on Base network. Use when you or the user want to trade, swap, exchange, buy, sell, or convert between tokens like USDC, ETH, and WETH. Covers phrases like "buy ETH", "sell ETH for USDC", "convert USDC to ETH".
user-invocable: true
disable-model-invocation: false
allowed-tools: ["Bash(npx fibx status*)", "Bash(npx fibx trade *)", "Bash(npx fibx balance*)"]
---

# Trading Tokens

Use the `fibx trade` command to swap tokens on Base network via Fibrous. You must be authenticated to trade.

## Confirm wallet is initialized and authed

```bash
npx fibx status
```

If the wallet is not authenticated, refer to the `authenticate-wallet` skill.

## Command Syntax

```bash
npx fibx trade <amount> <from> <to> [options]
```

## Arguments

| Argument | Description                                    |
| -------- | ---------------------------------------------- |
| `amount` | Amount to swap: '$1.00', '1.00', or raw units. |
| `from`   | Source token symbol (e.g., ETH, USDC)          |
| `to`     | Destination token symbol (e.g., USDC, WETH)    |

## Options

| Option           | Description                            |
| ---------------- | -------------------------------------- |
| `--slippage <n>` | Slippage tolerance percent (e.g., 0.5) |
| `--json`         | Output result as JSON                  |

## Examples

```bash
# Swap 0.01 ETH for USDC
npx fibx trade 0.01 ETH USDC

# Swap $10 USDC for ETH (assumes 1 USDC = $1)
npx fibx trade '$10' USDC ETH

# Swap with custom slippage (1%)
npx fibx trade 1 ETH USDC --slippage 1

# Get JSON output
npx fibx trade 1 ETH USDC --json
```

## Prerequisites

- Must be authenticated (`fibx status` to check)
- Wallet must have sufficient balance of the source token

## Error Handling

Common errors:

- "Not authenticated" - Run `fibx auth login <email>` first
- "No liquidity" - Try a smaller amount or different token pair
