---
name: send
description: Send ETH or ERC20 tokens (like USDC) to an Ethereum address. Use when you or the user want to send money, pay someone, transfer funds, tip, donate, or send to a wallet address. Covers phrases like "send $5", "send ETH", "transfer USDC".
user-invocable: true
disable-model-invocation: false
allowed-tools: ["Bash(npx fibx status*)", "Bash(npx fibx send *)", "Bash(npx fibx balance*)"]
---

# Sending Funds

Use the `fibx send` command to transfer ETH or ERC20 tokens from the wallet to any Ethereum address.

## Confirm wallet is initialized and authed

```bash
npx fibx status
```

If the wallet is not authenticated, refer to the `authenticate-wallet` skill.

## Command Syntax

```bash
npx fibx send <amount> <recipient> [token] [--json]
```

## Arguments

| Argument    | Description                                                                                                                 |
| ----------- | --------------------------------------------------------------------------------------------------------------------------- |
| `amount`    | Amount to send: '$1.00', '1.00', or raw units. Always single-quote amounts that use `$` to prevent bash variable expansion. |
| `recipient` | Ethereum address (0x...).                                                                                                   |
| `token`     | (Optional) Token symbol to send: `ETH`, `USDC`, etc. Defaults to `ETH` if omitted.                                          |

## Options

| Option   | Description           |
| -------- | --------------------- |
| `--json` | Output result as JSON |

## Examples

```bash
# Send 0.01 ETH (default token is ETH)
npx fibx send 0.01 0x1234...abcd

# Send 10 USDC
npx fibx send 10 0x1234...abcd USDC

# Send $1.00 USDC (assumes 1 USDC = $1)
npx fibx send '$1.00' 0x1234...abcd USDC

# Get JSON output
npx fibx send 0.1 0x1234...abcd ETH --json
```

## Prerequisites

- Must be authenticated (`fibx status` to check)
- Wallet must have sufficient balance (`fibx balance` to check)

## Error Handling

Common errors:

- "Not authenticated" - Run `fibx auth login <email>` first
- "Insufficient balance" - Check balance with `fibx balance`
- "Invalid recipient" - Must be valid 0x address
