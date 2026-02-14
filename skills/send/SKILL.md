---
name: send
description: Send ETH or ERC-20 tokens (like USDC) to an Ethereum address. Supports Base, Citrea, HyperEVM, and Monad.
license: MIT
compatibility: Requires Node.js and npx. Works with fibx CLI v0.1.2+.
metadata:
    version: 0.1.4
    author: ahmetenesdur
    category: detailed-transaction
allowed-tools:
    - Bash(npx fibx@latest send *)
    - Bash(npx fibx@latest status)
    - Bash(npx fibx@latest balance *)
    - Bash(npx fibx@latest tx-status *)
---

# Send Transaction

Use this skill to transfer assets. It handles both native ETH and ERC-20 tokens.

## Hard Rules (CRITICAL)

1.  **Pre-Flight Check**: Before ANY send operation, you **MUST** run:
    - `npx fibx@latest status` (to ensure connectivity)
    - `npx fibx@latest balance` (to ensure sufficient funds)
2.  **Recipient Confirmation**: If the user provides a recipient address that has **NOT** been mentioned in the current conversation history, you **MUST** ask for explicit confirmation before sending.
    - _Agent_: "I am about to send 10 USDC to 0x123...456. Is this correct?"
3.  **Chain Specification**:
    - If the user mentions a specific chain (e.g., "on Monad", "for my Citrea wallet"), you **MUST** include the `--chain <name>` parameter.
    - If the user **DOES NOT** mention a chain, you **MUST** either:
        - Explicitly state the default: "I will use **Base** for this transaction. Is that correct?"
        - OR ask for clarification: "Which chain would you like to use? Base, Citrea, HyperEVM, or Monad?"
4.  **Post-Flight Verification**: After the CLI returns a transaction hash, you **MUST** use the `tx-status` skill to verify it was included in a block and provide the explorer link to the user.

## Usage

```bash
npx fibx@latest send <amount> <recipient> [token] [--chain <chain>] [--json]
```

### Arguments

| Argument    | Description                                                                                      |
| ----------- | ------------------------------------------------------------------------------------------------ |
| `amount`    | Amount to send. Use simple numbers (e.g., `0.1`, `10`). Avoid `$` symbols in the command itself. |
| `recipient` | The destination Ethereum address (`0x...`).                                                      |
| `token`     | Optional. Token symbol (`ETH`, `USDC`, `WETH`). Defaults to `ETH`.                               |

### Options

| Option              | Description                                                             |
| ------------------- | ----------------------------------------------------------------------- |
| `--chain <network>` | Network to use: `base`, `citrea`, `hyperevm`, `monad`. Default: `base`. |
| `--json`            | Output result as JSON.                                                  |

## Examples

### Scenario: Sending USDC

**User:** "Send 10 USDC to 0x123...abc"

**Agent Actions:**

1.  `npx fibx@latest status` (Check auth)
2.  `npx fibx@latest balance` (Check funds)
3.  (If address is new) "Please confirm: Send 10 USDC to 0x123...abc?"
4.  User confirms.
5.  `npx fibx@latest send 10 0x123...abc USDC`
6.  `npx fibx@latest tx-status <hash_from_output>`

### Scenario: Sending ETH on Monad

**User:** "Send 0.05 MON to 0xdef...456 on Monad"

**Agent Actions:**

1.  `npx fibx@latest status --chain monad`
2.  `npx fibx@latest balance --chain monad`
3.  `npx fibx@latest send 0.05 0xdef...456 ETH --chain monad` (Native token is treated as 'ETH' generic or 'MON')
4.  `npx fibx@latest tx-status <hash> --chain monad`

## Error Handling

- **"Insufficient funds"**: Inform the user of their current balance.
- **"Invalid address"**: Ask the user to check the recipient address.
