---
name: send
description: Send ETH or ERC-20 tokens to another address. Supports Base, Citrea, HyperEVM, and Monad.
license: MIT
compatibility: Requires Node.js and npx. Works with fibx CLI v0.1.2+.
metadata:
    version: 0.2.0
    author: ahmetenesdur
    category: transaction
allowed-tools:
    - Bash(npx fibx@latest send *)
    - Bash(npx fibx@latest status)
    - Bash(npx fibx@latest balance *)
    - Bash(npx fibx@latest tx-status *)
---

# Send Transaction

Transfer assets (native ETH/MON or ERC-20 tokens) to a destination address.

## Hard Rules (CRITICAL)

1.  **Pre-Flight Check**: Before ANY send operation, you **MUST** run:
    - `npx fibx@latest status` (to ensure connectivity)
    - `npx fibx@latest balance` (to ensure sufficient funds)
2.  **Recipient Confirmation**: If the user provides a recipient address that has **NOT** been mentioned in the current conversation history, you **MUST** ask for explicit confirmation before sending.
    - _Agent_: "I am about to send 10 USDC to 0x123...456. Is this correct?"
3.  **Chain Specification**:
    - If the user mentions a specific chain, you **MUST** include the `--chain <name>` parameter.
    - If not mentioned, **MUST** clarify or state default (Base).
4.  **Post-Flight Verification**: You **MUST** use the `tx-status` skill to verify the transaction hash after sending.

## Input Schema

The agent should extract the following parameters:

| Parameter   | Type   | Description                            | Required             |
| :---------- | :----- | :------------------------------------- | :------------------- |
| `amount`    | number | Amount to send (e.g., `0.1`, `100`)    | Yes                  |
| `recipient` | string | Destination Ethereum address (`0x...`) | Yes                  |
| `token`     | string | Token symbol (`ETH`, `USDC`)           | No (Default: `ETH`)  |
| `chain`     | string | Network (`base`, `monad`, etc.)        | No (Default: `base`) |

## Usage

```bash
npx fibx@latest send <amount> <recipient> [token] [--chain <chain>] [--json]
```

## Options

| Option              | Description                                                             |
| ------------------- | ----------------------------------------------------------------------- |
| `--chain <network>` | Network to use: `base`, `citrea`, `hyperevm`, `monad`. Default: `base`. |
| `--json`            | Output result as JSON.                                                  |

## Examples

### Sending USDC

**User:** "Send 10 USDC to 0x123...abc"

**Agent Actions:**

1.  Check auth & balance.
2.  Confirm recipient.
3.  Send:
    ```bash
    npx fibx@latest send 10 0x123...abc USDC
    ```
4.  Verify:
    ```bash
    npx fibx@latest tx-status <hash>
    ```

### Sending ETH on Monad

**User:** "Send 0.05 MON to 0xdef...456 on Monad"

**Agent Actions:**

1.  Check status/balance on Monad.
2.  Send:
    ```bash
    npx fibx@latest send 0.05 0xdef...456 ETH --chain monad
    ```
3.  Verify on Monad:
    ```bash
    npx fibx@latest tx-status <hash> --chain monad
    ```

## Error Handling

- **"Insufficient funds"**: Inform user of current balance.
- **"Invalid address"**: Validate recipient format.
