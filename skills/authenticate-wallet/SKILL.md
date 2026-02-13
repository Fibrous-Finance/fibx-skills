---
name: authenticate-wallet
description: Sign in to the wallet. Use when you or the user want to log in, sign in, connect, or set up the wallet, or when any wallet operation fails with authentication or "not signed in" errors. This skill is a prerequisite before sending, trading, or funding.
user-invocable: true
disable-model-invocation: false
allowed-tools:
    ["Bash(fibx status*)", "Bash(fibx auth *)", "Bash(fibx balance*)", "Bash(fibx address*)"]
---

# Authenticating with the Wallet

When the wallet is not signed in (detected via `fibx status` or when wallet operations fail with authentication errors), use the `fibx` CLI to authenticate.

**Prerequisites**:
The following environment variables must be set in the runtime environment:

- `PRIVY_APP_ID`
- `PRIVY_APP_SECRET`

Authentication uses a two-step email OTP process:

## Authentication Flow

### Step 1: Initiate login

```bash
fibx auth login <email>
```

This sends a 6-digit verification code to the email.

### Step 2: Verify OTP

```bash
fibx auth verify <email> <code>
```

Use the email from step 1 and the 6-digit code from the user to complete authentication.

## Checking Authentication Status

```bash
fibx status
```

Displays wallet server health and authentication status including wallet address.

## Example Session

```bash
# Check current status
fibx status

# Start login (sends OTP to email)
fibx auth login user@example.com

# After user receives code, verify
fibx auth verify user@example.com 123456

# Confirm authentication
fibx status
```

## Available CLI Commands

| Command                           | Purpose                               |
| --------------------------------- | ------------------------------------- |
| `fibx status`                     | Check server health and auth status   |
| `fibx auth login <email>`         | Send OTP code to email                |
| `fibx auth verify <email> <code>` | Complete authentication with OTP code |
| `fibx balance`                    | Get wallet balance                    |
| `fibx address`                    | Get wallet address                    |

## JSON Output

All commands support `--json` for machine-readable output:

```bash
fibx status --json
fibx auth login user@example.com --json
fibx auth verify user@example.com 123456 --json
```
