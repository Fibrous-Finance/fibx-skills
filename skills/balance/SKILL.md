---
name: balance
description: Check wallet balances (ETH, USDC, etc.) on supported chains.
license: MIT
compatibility: Requires Node.js and npx. Works with fibx CLI v0.1.2+.
metadata:
    version: 0.2.0
    author: ahmetenesdur
    category: wallet-data
allowed-tools:
    - Bash(npx fibx@latest balance *)
    - Bash(npx fibx@latest status)
---

# Check Balance

Inspect the wallet's holdings (native ETH/MON and ERC-20 tokens).

## Hard Rules (CRITICAL)

1.  **Chain Specification**:
    - If the user mentions a specific chain (e.g., "on Monad", "for my Citrea wallet"), you **MUST** include the `--chain <name>` parameter.
    - If the user **DOES NOT** mention a chain, you **MUST** either:
        - Explicitly state the default: "I will check your balance on **Base**. Is that correct?"
        - OR ask for clarification: "Which chain would you like to check? Base, Citrea, HyperEVM, or Monad?"

## Input Schema

The agent should extract the following parameters:

| Parameter | Type   | Description                                              | Required             |
| :-------- | :----- | :------------------------------------------------------- | :------------------- |
| `chain`   | string | Network to check (`base`, `citrea`, `hyperevm`, `monad`) | No (Default: `base`) |

## Usage

```bash
npx fibx@latest balance [--chain <chain>] [--json]
```

## Options

| Option              | Description                                                               |
| ------------------- | ------------------------------------------------------------------------- |
| `--chain <network>` | Network to check: `base`, `citrea`, `hyperevm`, `monad`. Default: `base`. |
| `--json`            | Output results in JSON format.                                            |

## Examples

### Check Base Balance (Default)

```bash
npx fibx@latest balance
```

### Check Monad Balance

```bash
npx fibx@latest balance --chain monad
```

## Error Handling

- **"Not authenticated"**: Run `authenticate-wallet` skill.
- **"Network error"**: Retry the command once.
