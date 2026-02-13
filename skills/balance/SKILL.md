---
name: balance
description: Check wallet balances (ETH, USDC, etc.) on supported chains (Base, Citrea, HyperEVM, Monad).
license: MIT
compatibility: Requires Node.js and npx. Works with fibx CLI v0.1.2+.
metadata:
    version: 0.1.3
    author: ahmetenesdur
    category: wallet-data
allowed-tools:
    - Bash(npx fibx balance *)
    - Bash(npx fibx status)
---

# Check Balance

Use this skill to inspect the wallet's holdings. By default, it checks the Base network, but can be directed to others.

## usage

```bash
npx fibx balance [--chain <chain>] [--json]
```

## Options

| Option              | Description                                                               |
| ------------------- | ------------------------------------------------------------------------- |
| `--chain <network>` | Network to check: `base`, `citrea`, `hyperevm`, `monad`. Default: `base`. |
| `--json`            | Output results in JSON format.                                            |

## Examples

### Check Base Balance (Default)

```bash
npx fibx balance
```

### Check Monad Balance

```bash
npx fibx balance --chain monad
```

### Get Raw Data

```bash
npx fibx balance --json
```

## Error Handling

- **"Not authenticated"**: Run `authenticate-wallet` skill.
- **"Network error"**: Retry the command once.
