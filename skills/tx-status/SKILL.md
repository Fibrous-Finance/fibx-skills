---
name: tx-status
description: Check the status of a transaction hash, get receipt details, and view the block explorer link. Use when you have a transaction hash and need to verify if it succeeded or failed, or to provide a proof link to the user.
license: MIT
compatibility: Requires Node.js and npx. Works with fibx CLI v0.1.2+.
metadata:
    version: 0.1.3
    author: ahmetenesdur
    category: utility
allowed-tools:
    - Bash(npx fibx tx-status *)
---

# Transaction Status Verification

Use this skill to fetch on-chain data for a given transaction hash. This is critical for verifying the outcome of `send` or `trade` operations.

## Hard Rules (CRITICAL)

1.  **Verification**: After performing a `send` or `trade`, you MUST use this skill if the user asks "did it go through?" or "show me the link".
2.  **Chain Specificity**: If the transaction was on a specific chain (e.g., Monad), you MUST verify the status on that same chain using the `--chain` flag.

## Usage

### Check Status

```bash
npx fibx tx-status <hash> [--chain <chain>]
```

- `<hash>`: The transaction hash (starting with `0x`).
- `--chain <chain>`: Optional. The network the transaction was sent on (`base`, `citrea`, `hyperevm`, `monad`). Defaults to `base`.

## Examples

### Scenario 1: Check a Base transaction (Default)

**Input:** "Check status of 0x123...456"

```bash
npx fibx tx-status 0x123...456
```

### Scenario 2: Check a Monad transaction

**Input:** "Verify tx 0xabc...def on Monad"

```bash
npx fibx tx-status 0xabc...def --chain monad
```

## JSON Output (for programmatic parsing)

Use `--json` to get a machine-readable response:

```bash
npx fibx tx-status 0x123...456 --json
```

**Output Schema:**

```json
{
	"status": "success", // "success" or "reverted"
	"blockNumber": "12345", // Block number where included
	"gasUsed": "21000", // Gas used
	"explorerLink": "https://..." // URL to block explorer
}
```

## Error Handling

- **Transaction not found**: If the TX is not found, ensure you are querying the correct chain.
- **Pending**: The CLI may hang or return "pending" if the tx is still in the mempool.
