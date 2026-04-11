---
name: authenticate-wallet
description: Authenticate the fibx CLI wallet via email OTP (Privy) or private key import. Required before any wallet operation (balance, send, trade, aave). Private keys are encrypted at rest with AES-256-GCM.
license: MIT
compatibility: Requires Node.js 18+ and npx. Uses `npx fibx@latest`.
metadata:
    version: 0.6.0
    author: ahmetenesdur
    category: auth
allowed-tools:
    - Bash(npx fibx@latest auth login *)
    - Bash(npx fibx@latest auth verify *)
    - Bash(npx fibx@latest auth import)
    - Bash(npx fibx@latest auth logout)
    - Bash(npx fibx@latest status)
---

# Wallet Authentication

Manage the authentication session for the `fibx` CLI. Supports two methods: email OTP (via Privy server wallets) and private key import (local wallet).

## Prerequisites

- No active session required — this skill creates one

## Rules

1. For email login, NEVER ask the user for a private key.
2. For private key import, warn the user: _"Your private key will be encrypted with AES-256-GCM and stored locally. The encryption key is auto-generated per machine. Shall I proceed?"_
3. You MUST complete `auth login` before `auth verify`. They are sequential steps.
4. After successful `auth verify` or `auth import`, ALWAYS run `npx fibx@latest status` to confirm the session is active.
5. NEVER store or log private keys, OTP codes, or session tokens in conversation history.

## Commands

### Email OTP Login (2-step)

```bash
# Step 1: Send OTP to email
npx fibx@latest auth login <email>

# Step 2: Verify OTP code
npx fibx@latest auth verify <email> <code>
```

### Private Key Import

```bash
npx fibx@latest auth import
```

> **INTERACTIVE COMMAND**: This opens a prompt for the user to paste their private key. The agent CANNOT pass the key as a CLI argument. Instruct the user to enter it in the terminal prompt, or run it via the agent's terminal tool and let the user type the key.

### Session Management

```bash
# Check current session status
npx fibx@latest status

# End session
npx fibx@latest auth logout
```

## Parameters

| Parameter | Type   | Description                          | Required          |
| --------- | ------ | ------------------------------------ | ----------------- |
| `email`   | string | User's email address                 | Yes (email OTP)   |
| `code`    | string | One-time password received via email | Yes (verify step) |

## Session Details

- **Privy sessions**: JWT-based, 7-day expiry. After expiry, the user must re-authenticate via `auth login`.
- **Private key sessions**: No expiry. Session persists until `auth logout`.
- **Storage**: Sessions are stored in an OS-dependent config directory (e.g. `~/.config/fibx-nodejs/session.json` on Linux, `~/Library/Preferences/fibx-nodejs/session.json` on macOS).
- **Encryption**: Private keys are encrypted at rest using AES-256-GCM. The encryption key is auto-generated per machine in the OS config directory (e.g. `~/.config/fibx-nodejs/encryption-key` on Linux). Legacy plaintext keys are auto-migrated on first load.
- **CI/Docker**: Set `FIBX_SESSION_SECRET` environment variable (64-char hex) to use a custom encryption key instead of the auto-generated one.

## Examples

**User:** "Log me in with user@example.com"

```bash
npx fibx@latest auth login user@example.com
# Wait for user to provide the OTP code (e.g. "123456")
npx fibx@latest auth verify user@example.com 123456
npx fibx@latest status
```

**User:** "Import my private key"

```bash
# Warn user first, then:
npx fibx@latest auth import
npx fibx@latest status
```

**User:** "Log me out"

```bash
npx fibx@latest auth logout
```

## Error Handling

| Error               | Action                                                 |
| ------------------- | ------------------------------------------------------ |
| `Invalid code`      | Ask the user to check their email and retry `verify`.  |
| `Rate limit`        | Wait 60 seconds before retrying.                       |
| `Session expired`   | Privy JWT expired (7 days). Restart from `auth login`. |
| `Not authenticated` | Run the full login flow before other skills.           |

## Related Skills

- Use `status` to verify your session is active before any operation.
- Run `balance` after authentication to see available funds.
- Run `portfolio` after authentication to see a full cross-chain overview with USD values.
