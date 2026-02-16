---
name: tx-status
description: Check transaction status, receipt details, and explorer link.
license: MIT
compatibility: Requires Node.js and npx. Works with fibx CLI v0.2.6+.
metadata:
    version: 0.2.6
    author: ahmetenesdur
    category: utility
allowed-tools:
    - Bash(npx fibx@latest tx-status *)
---

# Transaction Status

Fetch on-chain data for a given transaction hash to verify success/failure.

## Hard Rules (CRITICAL)

1.  **Verification**: After performing a `send` or `trade`, use this skill if the user asks "did it go through?" or "show me the link".
2.  **Chain Specificity**: If the transaction was on a specific chain (e.g., Monad), you **MUST** verify the status on that same chain using the `--chain` flag.

## Input Schema

The agent should extract the following parameters:

| Parameter | Type   | Description                         | Required             |
| :-------- | :----- | :---------------------------------- | :------------------- |
| `hash`    | string | Transaction hash (starts with `0x`) | Yes                  |
| `chain`   | string | Network (`base`, `monad`, etc.)     | No (Default: `base`) |

## Usage

```bash
npx fibx@latest tx-status <hash> [--chain <chain>] [--json]
```

## Options

| Option              | Description                                                      |
| ------------------- | ---------------------------------------------------------------- |
| `--chain <network>` | Network: `base`, `citrea`, `hyperevm`, `monad`. Default: `base`. |
| `--json`            | Output result as JSON.                                           |

## Examples

### Check Base Transaction

**Input:** "Check status of 0x123...456"

```bash
npx fibx@latest tx-status 0x123...456
```

### Check Monad Transaction

**Input:** "Verify tx 0xabc...def on Monad"

```bash
npx fibx@latest tx-status 0xabc...def --chain monad
```

## JSON Output

```json
{
	"status": "success",
	"blockNumber": "12345",
	"gasUsed": "21000",
	"explorerLink": "https://..."
}
```

## Cross-Skill Integration

- **Post-Flight for `send` and `trade`**: This skill is the verification step after any on-chain transaction. Always pass the same `--chain` flag used in the original command.

## Error Handling

- **"Transaction not found"**: Ensure querying the correct chain.
- **"Pending"**: TX might still be in mempool.
