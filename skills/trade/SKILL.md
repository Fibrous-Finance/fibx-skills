---
name: trade
description: Swap/Trade tokens using Fibrous Finance aggregation. Supports Base, Citrea, HyperEVM, and Monad.
license: MIT
compatibility: Requires Node.js and npx. Works with fibx CLI v0.1.2+.
metadata:
    version: 0.2.0
    author: ahmetenesdur
    category: transaction
allowed-tools:
    - Bash(npx fibx@latest trade *)
    - Bash(npx fibx@latest status)
    - Bash(npx fibx@latest balance *)
    - Bash(npx fibx@latest tx-status *)
---

# Trade / Swap Tokens

Exchange one token for another using the Fibrous Finance aggregator to find the best route.

## Hard Rules (CRITICAL)

1.  **Pre-Flight Check**: Before ANY trade, you **MUST** run:
    - `npx fibx@latest status`
    - `npx fibx@latest balance` (ensure you have the _source_ token)
2.  **Chain Specification**:
    - If the user mentions a specific chain, you **MUST** include the `--chain <name>` parameter.
    - If not mentioned, **MUST** clarify or default to **Base**.
3.  **Slippage Safety**: The default slippage is **0.5%**. If you need to change this (e.g., for volatile tokens), you **MUST** ask the user for confirmation first.
4.  **Approval Limits**: The CLI defaults to "Exact Approval". Do **NOT** use `--approve-max` unless explicitly requested.
5.  **Simulation & Verification**: The CLI performs a route check. If this fails, do not proceed. After swapping, verify with `tx-status`.

## Input Schema

The agent should extract the following parameters:

| Parameter    | Type   | Description                      | Required             |
| :----------- | :----- | :------------------------------- | :------------------- |
| `amount`     | number | Amount to swap (e.g., `1.5`)     | Yes                  |
| `from_token` | string | Source token (e.g., `ETH`)       | Yes                  |
| `to_token`   | string | Destination token (e.g., `USDC`) | Yes                  |
| `chain`      | string | Network (`base`, `monad`, etc.)  | No (Default: `base`) |
| `slippage`   | number | Slippage tolerance (e.g., `1.0`) | No (Default: `0.5`)  |

## Usage

```bash
npx fibx@latest trade <amount> <from_token> <to_token> [options]
```

## Options

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

1.  Check status/balance.
2.  Trade:
    ```bash
    npx fibx@latest trade 0.1 ETH USDC
    ```
3.  Verify:
    ```bash
    npx fibx@latest tx-status <hash>
    ```

### Swap on Monad

**User:** "Buy USDC with 1 MON on Monad"

**Agent Actions:**

1.  Check status/balance on Monad.
2.  Trade:
    ```bash
    npx fibx@latest trade 1 MON USDC --chain monad
    ```
3.  Verify:
    ```bash
    npx fibx@latest tx-status <hash> --chain monad
    ```

## Error Handling

- **"No route found"**: The trade path might not exist or liquidity is too low.
- **"Insufficient balance"**: Check `balance` again.
- **"Slippage exceeded"**: Logic moved unfavorably; suggest retrying with higher slippage.
