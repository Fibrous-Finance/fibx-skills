---
name: authenticate-wallet
description: Choose how the fibx CLI signs and create the session — the user's own wallet over WalletConnect, a Privy server wallet via email OTP, or an imported private key (AES-256-GCM at rest). Required before any wallet operation (balance, send, trade, aave).
license: MIT
compatibility: Requires Node.js 18+ and npx. Uses `npx fibx@latest`.
metadata:
    version: 0.10.0
    author: ahmetenesdur
    category: auth
allowed-tools:
    - Bash(npx fibx@latest auth setup)
    - Bash(npx fibx@latest auth connect *)
    - Bash(npx fibx@latest auth login *)
    - Bash(npx fibx@latest auth verify *)
    - Bash(npx fibx@latest auth import)
    - Bash(npx fibx@latest auth logout)
    - Bash(npx fibx@latest status)
---

# Wallet Authentication

Manage the `fibx` CLI's session. There are three signing paths, and they are
peers — not a default with fallbacks:

| Path                 | Command                      | Key held by               | Runs while the user is away                  | Bounded by                                   |
| -------------------- | ---------------------------- | ------------------------- | -------------------------------------------- | -------------------------------------------- |
| Your own wallet      | `auth connect`               | the user's wallet app     | no — every transaction is approved on phone  | the wallet, plus the local signing policy    |
| Privy server wallet  | `auth login` + `auth verify` | Privy, server-side        | yes                                          | Privy's signing policy, plus the local one   |
| Imported private key | `auth import`                | this machine, encrypted   | yes                                          | **the local signing policy alone**           |

`auth setup` asks the user which they want and explains the trade-offs.
Prefer it when the user has not said.

## Prerequisites

- None — this skill creates the session.

## Rules

1. If the user has not said how they want to sign, run `auth setup` (it is
   interactive) or ask. Do NOT default to Privy.
2. NEVER ask the user for a private key. `auth import` prompts for it in the
   terminal; the agent cannot pass it as an argument.
3. Before `auth import`, warn: _"Your private key will be encrypted with
   AES-256-GCM and stored on this machine. With an imported key, the local
   signing policy is the only thing bounding what this wallet signs — I can
   set one with the `policy` skill. Proceed?"_
4. `auth login` before `auth verify`. They are sequential.
5. `auth connect` prints a QR code and a `wc:` URI, then waits. Tell the user
   to scan it with their wallet app (or paste the URI into it) and approve the
   pairing. From then on **every transaction is approved on their phone** — a
   `trade` or `send` blocks until they do, and a rejection there is normal.
6. After any of these, run `status` to confirm the session is active.
7. NEVER store or log private keys, OTP codes, pairing URIs or session data
   in the conversation.
8. Any of these **replaces** the active session. Say so before switching.

## Commands

### Choose a path (interactive)

```bash
npx fibx@latest auth setup
```

### Your own wallet, over WalletConnect

```bash
npx fibx@latest auth connect
```

> **INTERACTIVE**: shows a QR code and a `wc:` URI and waits for the wallet
> app to approve. `--json` prints `{ "uri": "wc:…" }` instead of the QR.

### Email OTP login (2-step)

```bash
# Step 1: Send OTP to email
npx fibx@latest auth login <email>

# Step 2: Verify OTP code
npx fibx@latest auth verify <email> <code>
```

### Private key import

```bash
npx fibx@latest auth import
```

> **INTERACTIVE**: opens a prompt for the user to paste their private key.
> Instruct the user to type it in the terminal; never relay it.

### Session management

```bash
# Check current session status
npx fibx@latest status

# End the session (also ends a WalletConnect pairing)
npx fibx@latest auth logout
```

## Parameters

| Parameter | Type   | Description                          | Required          |
| --------- | ------ | ------------------------------------ | ----------------- |
| `email`   | string | User's email address                 | Yes (email OTP)   |
| `code`    | string | One-time password received via email | Yes (verify step) |

## Session Details

- **WalletConnect sessions**: the pairing persists until `auth logout` or the
  wallet disconnects. Nothing is signed on this machine.
- **Privy sessions**: JWT-based, 7-day expiry. After expiry, re-authenticate
  via `auth login`.
- **Private key sessions**: no expiry. Persist until `auth logout`.
- **Storage**: an OS-dependent config directory (e.g.
  `~/.config/fibx-nodejs/session.json` on Linux,
  `~/Library/Preferences/fibx-nodejs/session.json` on macOS). The local
  signing policy, if set, sits beside it as `policy.json`.
- **Encryption**: imported keys are encrypted at rest with AES-256-GCM. The
  encryption key is auto-generated per machine in the same directory.
- **CI/Docker**: set `FIBX_SESSION_SECRET` (64-char hex) to use a custom
  encryption key instead of the auto-generated one.

## Examples

**User:** "Which should I use?"

```bash
npx fibx@latest auth setup
```

**User:** "Connect my own wallet" / "Use my MetaMask"

```bash
npx fibx@latest auth connect
# Ask the user to scan the QR with their wallet and approve
npx fibx@latest status
```

**User:** "Log me in with user@example.com"

```bash
npx fibx@latest auth login user@example.com
# Wait for the user to provide the OTP code (e.g. "123456")
npx fibx@latest auth verify user@example.com 123456
npx fibx@latest status
```

**User:** "Import my private key"

```bash
# Warn first (rule 3), then:
npx fibx@latest auth import
npx fibx@latest status
```

**User:** "Log me out"

```bash
npx fibx@latest auth logout
```

## Error Handling

| Error                            | Action                                                                        |
| -------------------------------- | ----------------------------------------------------------------------------- |
| `Invalid code`                   | Ask the user to check their email and retry `verify`.                         |
| `Rate limit`                     | Wait 60 seconds before retrying.                                              |
| `Session expired`                | Privy JWT expired (7 days). Restart from `auth login`.                        |
| Pairing timed out / rejected     | The wallet did not approve. Run `auth connect` again when the user is ready.  |
| `Not authenticated`              | Run one of the flows above before other skills.                               |
| `POLICY_BLOCKED`                 | The local signing policy refused it. Use the `policy` skill; do not retry.    |

## Related Skills

- `policy` — bound what this session may sign; essential on the imported-key path.
- `wallet-info` — see which path is active and the address.
- `balance` / `portfolio` — after authentication, see available funds.
