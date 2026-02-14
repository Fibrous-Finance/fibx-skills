---
name: trade
description: Swap/Trade tokens using Fibrous Finance aggregation. Supports Base, Citrea, HyperEVM, and Monad.
license: MIT
compatibility: Requires Node.js and npx. Works with fibx CLI v0.1.2+.
metadata:
    version: 0.1.4
    author: ahmetenesdur
    category: detailed-transaction
allowed-tools:
    - Bash(npx fibx@latest trade *)
    - Bash(npx fibx@latest status)
    - Bash(npx fibx@latest balance *)
    - Bash(npx fibx@latest tx-status *)
---

# Trade / Swap Tokens

Use this skill to exchange one token for another. It uses the Fibrous Finance aggregator to find the best route.

## Hard Rules (CRITICAL)

1.  **Pre-Flight Check**: Before ANY trade, you **MUST** run:
    - `npx fibx@latest status`
    - `npx fibx@latest balance` (ensure you have the _source_ token)
2.  **Chain Specification**:
    - If the user mentions a specific chain (e.g., "on Monad", "for my Citrea wallet"), you **MUST** include the `--chain <name>` parameter.
    - If the user **DOES NOT** mention a chain, you **MUST** either:
        - Explicitly state the default: "I will perform this trade on **Base**. Is that correct?"
        - OR ask for clarification: "Which chain would you like to trade on? Base, Citrea, HyperEVM, or Monad?"
3.  **Slippage Safety**: The default slippage is **0.5%**. If you need to change this (e.g., for volatile tokens), you **MUST** ask the user for confirmation first.
4.  **Approval Limits**: The CLI defaults to "Exact Approval" (approves only the amount to be swapped).
    - Do **NOT** use `--approve-max` unless the user explicitly requests "infinite approval" or "max approval".
5.  **Simulation & Verification**: The CLI performs a route check before swapping. If this fails, do not proceed. After swapping, verify the transaction with the `tx-status` skill.

## Usage

```bash
npx fibx@latest trade <amount> <from_token> <to_token> [options]
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

1.  `npx fibx@latest status`
2.  `npx fibx@latest balance`
3.  `npx fibx@latest trade 0.1 ETH USDC`
4.  `npx fibx@latest tx-status <hash>`

### Swap with Custom Slippage

**User:** "Swap 1000 DEGEN to ETH with 2% slippage"

**Agent Actions:**

1.  Checks info.
2.  **Agent**: "Confirming: You want to swap 1000 DEGEN to ETH with **2%** slippage. Is this correct?"
3.  User confirms.
4.  `npx fibx@latest trade 1000 DEGEN ETH --slippage 2`

### Swap on Monad

**User:** "Buy USDC with 1 MON on Monad"

**Agent Actions:**

1.  `npx fibx@latest status --chain monad`
2.  `npx fibx@latest trade 1 MON USDC --chain monad`

## Error Handling

- **"No route found"**: The trade path might not exist or liquidity is too low.
- **"Insufficient balance"**: Check `balance` again.
- **"Slippage exceeded"**: The price moved unfavorably; suggest retrying with higher slippage (after user confirmation).
