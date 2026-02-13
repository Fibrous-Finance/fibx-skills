---
name: trade
description: Swap/Trade tokens using Fibrous Finance aggregation. Supports Base, Citrea, HyperEVM, and Monad.
license: MIT
compatibility: Requires Node.js and npx. Works with fibx CLI v0.1.2+.
metadata:
    version: 0.1.3
    author: ahmetenesdur
    category: detailed-transaction
allowed-tools:
    - Bash(npx fibx trade *)
    - Bash(npx fibx status)
    - Bash(npx fibx balance *)
    - Bash(npx fibx tx-status *)
---

# Trade / Swap Tokens

Use this skill to exchange one token for another. It uses the Fibrous Finance aggregator to find the best route.

## Hard Rules (CRITICAL)

1.  **Pre-Flight Check**: Before ANY trade, you **MUST** run:
    - `npx fibx status`
    - `npx fibx balance` (ensure you have the _source_ token)
2.  **Slippage Safety**: The default slippage is **0.5%**. If you need to change this (e.g., for volatile tokens), you **MUST** ask the user for confirmation first.
3.  **Approval Limits**: The CLI defaults to "Exact Approval" (approves only the amount to be swapped).
    - Do **NOT** use `--approve-max` unless the user explicitly requests "infinite approval" or "max approval".
4.  **Simulation & Verification**: The CLI performs a route check before swapping. If this fails, do not proceed. After swapping, verify the transaction with the `tx-status` skill.

## Usage

```bash
npx fibx trade <amount> <from_token> <to_token> [options]
```

### Arguments

| Argument     | Description                                          |
| ------------ | ---------------------------------------------------- |
| `amount`     | Amount to swap (e.g., `1.5`, `100`).                 |
| `from_token` | Source token symbol (`ETH`, `USDC`) or address.      |
| `to_token`   | Destination token symbol (`WETH`, `DAI`) or address. |

### Options

| Option              | Description                                                      |
| ------------------- | ---------------------------------------------------------------- |
| `--chain <network>` | Network: `base`, `citrea`, `hyperevm`, `monad`. Default: `base`. |
| `--slippage <n>`    | Slippage tolerance in percentage (e.g., `1.0`). Default `0.5`.   |
| `--approve-max`     | Force infinite approval. **Use with caution.**                   |
| `--json`            | Output result as JSON.                                           |

## Examples

### Standard Swap (Base)

**User:** "Swap 0.1 ETH for USDC"

**Agent Actions:**

1.  `npx fibx status`
2.  `npx fibx balance`
3.  `npx fibx trade 0.1 ETH USDC`
4.  `npx fibx tx-status <hash>`

### Swap with Custom Slippage

**User:** "Swap 1000 DEGEN to ETH with 2% slippage"

**Agent Actions:**

1.  Checks info.
2.  **Agent**: "Confirming: You want to swap 1000 DEGEN to ETH with **2%** slippage. Is this correct?"
3.  User confirms.
4.  `npx fibx trade 1000 DEGEN ETH --slippage 2`

### Swap on Monad

**User:** "Buy USDC with 1 MON on Monad"

**Agent Actions:**

1.  `npx fibx status --chain monad`
2.  `npx fibx trade 1 MON USDC --chain monad`

## Error Handling

- **"No route found"**: The trade path might not exist or liquidity is too low.
- **"Insufficient balance"**: Check `balance` again.
- **"Slippage exceeded"**: The price moved unfavorably; suggest retrying with higher slippage (after user confirmation).
