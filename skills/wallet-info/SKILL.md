---
name: wallet-info
description: Show the active wallet address, wallet ID, session type, and creation time.
license: MIT
compatibility: Requires Node.js 18+ and npx. Uses `npx fibx@latest`.
metadata:
    version: 0.7.0
    author: ahmetenesdur
    category: wallet-data
allowed-tools:
    - Bash(npx fibx@latest address)
    - Bash(npx fibx@latest address *)
    - Bash(npx fibx@latest wallets)
    - Bash(npx fibx@latest wallets *)
---

# Wallet Info

Report which wallet is currently active. Use this when the user asks "what is my
address", "which wallet am I using", or needs an address to receive funds.

## Prerequisites

- Active session required. If not authenticated, run `authenticate-wallet` skill first.

## Rules

1. Use `address` when the user only needs the receiving address.
2. Use `wallets` when the user asks about the session itself — which wallet, what
   kind of session, since when.
3. Neither command takes a `--chain` flag. The same address is used on every
   supported chain, so do NOT pass `--chain`.
4. Always show the FULL address. Never truncate an address the user may need to
   copy for a transfer.
5. Use `--json` when the output will be consumed by another skill or pipeline.

## Commands

```bash
npx fibx@latest address [--json]
npx fibx@latest wallets [--json]
```

## Parameters

| Parameter | Type | Description    | Required |
| --------- | ---- | -------------- | -------- |
| `json`    | flag | Output as JSON | No       |

## JSON Output Structure

`address`:

```json
{
	"address": "0x...",
	"walletId": "..."
}
```

`wallets`:

```json
{
	"address": "0x...",
	"walletId": "...",
	"type": "privy",
	"createdAt": "2026-01-01T00:00:00.000Z"
}
```

`type` is `privy` for an email login (server-side signing) or `private-key` for
an imported key (local signing).

## Examples

**User:** "What's my wallet address?"

```bash
npx fibx@latest address
```

**User:** "Which wallet am I logged in with?"

```bash
npx fibx@latest wallets
```

## Error Handling

| Error               | Action                                                       |
| ------------------- | ------------------------------------------------------------ |
| `Not authenticated` | Run `authenticate-wallet` skill first.                       |
| `No active session` | `wallets` reports this instead of failing — offer to log in. |
| `Session expired`   | Run `authenticate-wallet` skill to re-authenticate.          |

## Related Skills

- Run `authenticate-wallet` first if there is no session.
- Use `balance` to see holdings for this wallet on one chain.
- Use `portfolio` for a cross-chain overview with USD valuations.
