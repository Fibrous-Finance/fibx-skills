---
name: authenticate-wallet
description: Sign in to the wallet using email OTP (Privy) or Import a Private Key. Required for all private wallet operations.
license: MIT
compatibility: Requires Node.js and npx. Works with fibx CLI v0.2.6+.
metadata:
    version: 0.2.6
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

Manage the authentication session for the `fibx` CLI using a 2-step email OTP process.

## Hard Rules (CRITICAL)

1.  **Safety First**:
    - For **Email Login**: NEVER ask for private keys.
    - For **Private Key Import**: WARN the user that the key will be stored locally. "I can import your private key, but please note it will be stored on your local machine. Shall I proceed?"
2.  **Sequential Flow**: You MUST complete `auth login` (Step 1) before `auth verify` (Step 2).
3.  **Verification**: After `auth verify` or `auth import`, ALWAYS run `npx fibx@latest status` to confirm the session is active.

## Input Schema

The agent should extract the following parameters:

| Parameter | Type   | Description                          | Required         |
| :-------- | :----- | :----------------------------------- | :--------------- |
| `email`   | string | User's email address                 | Yes              |
| `otp`     | string | One-time password received via email | Yes (for verify) |

## Usage

### Step 1: Initiate Login (Email)

```bash
npx fibx@latest auth login <email>
```

### Step 2: Verify OTP (Email)

```bash
npx fibx@latest auth verify <email> <code>
```

### Alternative: Import Private Key

```bash
npx fibx@latest auth import
```

> **⚠️ INTERACTIVE COMMAND**: This command opens an interactive prompt. The agent **CANNOT** pass the private key as a CLI argument. You must instruct the user to run this command themselves in their terminal, or run it via the agent's terminal tool and let the user type the key into the prompt.

_Note: The private key is stored locally in an encrypted session file._

### Check Status

```bash
npx fibx@latest status
```

### Logout

```bash
npx fibx@latest auth logout
```

## Examples

### Full Login Flow

**User:** "Log me in with user@example.com"

**Agent Actions:**

1.  Initiate login:
    ```bash
    npx fibx@latest auth login user@example.com
    ```
2.  (User provides code "123456")
3.  Verify code:
    ```bash
    npx fibx@latest auth verify user@example.com 123456
    ```
4.  Confirm status:
    ```bash
    npx fibx@latest status
    ```

## Error Handling

- **"Invalid code"**: Ask the user to check the email and try `verify` again.
- **"Rate limit"**: Wait for a few seconds before retrying.
- **"Session expired"**: Restart the flow from Step 1.
