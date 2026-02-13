---
name: authenticate-wallet
description: Sign in to the wallet using email OTP. Use this when the user needs to log in, or when other skills fail with "not authenticated" errors.
license: MIT
compatibility: Requires Node.js and npx. Works with fibx CLI v0.1.2+.
metadata:
    version: 0.1.3
    author: ahmetenesdur
    category: detailed-auth
allowed-tools:
    - Bash(npx fibx auth *)
    - Bash(npx fibx status *)
---

# Wallet Authentication

This skill manages the authentication session for the `fibx` CLI. It uses a 2-step email OTP process managed by Privy.

## Usage

### Step 1: Initiate Login

Request an OTP code to be sent to the user's email.

```bash
npx fibx auth login <email>
```

### Step 2: Verify OTP

Complete the login using the code provided by the user.

```bash
npx fibx auth verify <email> <code>
```

### Check Status

Verify if the wallet is currently authenticated and view the wallet address.

```bash
npx fibx status
```

## Examples

### Full Login Flow

1.  **Agent**: "I need to log you in. What is your email?"
2.  **User**: "user@example.com"
3.  **Agent**: Runs command:
    ```bash
    npx fibx auth login user@example.com
    ```
4.  **Agent**: "I've sent a code to your email. Please provide it."
5.  **User**: "123456"
6.  **Agent**: Runs command:
    ```bash
    npx fibx auth verify user@example.com 123456
    ```
7.  **Agent**: Validates success:
    ```bash
    npx fibx status
    ```

## Error Handling

- **"Invalid code"**: Ask the user to check the code and try `verify` again.
- **"Rate limit"**: Wait for a few seconds before retrying.
- **"Session expired"**: Restart the flow from Step 1.
