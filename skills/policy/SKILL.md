---
name: policy
description: Read and change the fibx CLI's local signing policy — a file on the user's machine that caps the native value per chain, allowlists chains and destinations, and expires. Enforced before every signature on all three signing paths. Use when a transaction is refused with POLICY_BLOCKED, or when the user wants to bound what the wallet can sign.
license: MIT
compatibility: Requires Node.js 18+ and npx. Uses `npx fibx@latest`.
metadata:
    version: 0.10.0
    author: ahmetenesdur
    category: auth
allowed-tools:
    - Bash(npx fibx@latest policy *)
---

# Signing Policy

A JSON file the user owns, beside the session (`policy.json` in the config
directory). FibX evaluates it before signing anything — with an imported key,
through Privy, or over WalletConnect alike. The rules:

| Rule                         | Meaning                                                | Example                                   |
| ---------------------------- | ------------------------------------------------------ | ----------------------------------------- |
| `<chain>.maxValue`           | Largest native value (ETH/HYPE/MON) in one transaction | `base.maxValue 0.05`                      |
| `allowedChains`              | Chains anything may be signed on                       | `allowedChains base,monad`                |
| `<chain>.allowedDestinations` | Addresses anything may be sent to on that chain        | `base.allowedDestinations 0xAbC…,0xDeF…`  |
| `expiry`                     | When the whole policy stops applying                   | `expiry 2026-12-31T00:00:00Z`             |

No policy file means **everything is permitted**. A file that exists but
cannot be read means **everything is refused** until it is fixed or cleared.

## Prerequisites

- None. The policy can be set before any session exists.

## Rules

1. **Propose, then set.** Suggest a concrete value and explain what it bounds;
   set it only after the user agrees.
2. **`maxValue` bounds native value only.** An ERC-20 transfer reaches the
   policy with `value: 0`, so the cap never sees a token amount — only
   `allowedDestinations` bounds where tokens go. Never tell the user the cap
   limits token amounts.
3. **On the imported-key path this file is the only bound.** If `status`
   shows a private-key session and `policy show` shows nothing, recommend at
   least a `maxValue` before any `trade` or `send`.
4. **A refusal names its rule.** `POLICY_BLOCKED` comes with the rule and the
   limit. Relay both and ask; do not retry, and do not loosen the rule
   unasked.
5. `policy clear` with no rule removes the whole policy and asks for
   confirmation. Do not pass `-y` unless the user explicitly asked to remove
   everything.
6. Never edit the file by hand; the CLI validates what it writes.

## Commands

```bash
# Show the active policy (or that there is none)
npx fibx@latest policy show

# Set one rule (replaces the rule's previous value)
npx fibx@latest policy set <rule> <value>

# Remove one rule
npx fibx@latest policy clear <rule>

# Remove the whole policy (asks for confirmation)
npx fibx@latest policy clear
```

## Parameters

| Parameter | Type   | Description                                                                    | Required |
| --------- | ------ | ------------------------------------------------------------------------------ | -------- |
| `rule`    | string | `expiry`, `allowedChains`, `<chain>.maxValue` or `<chain>.allowedDestinations` | Yes      |
| `value`   | string | The value; comma-separated for lists; native units for `maxValue`             | Yes      |
| `chain`   | string | `base`, `hyperevm`, or `monad`                                                 | in rule  |

## Examples

**User:** "Cap what this wallet can sign to 0.05 ETH on Base."

```bash
npx fibx@latest policy set base.maxValue 0.05
npx fibx@latest policy show
```

**User:** "Only let it use Base and Monad."

```bash
npx fibx@latest policy set allowedChains base,monad
```

**User:** "Only allow sends to my hardware wallet, 0xAbC…"

```bash
npx fibx@latest policy set base.allowedDestinations 0xAbC...
```

**User:** "What limits are on my wallet?"

```bash
npx fibx@latest policy show
```

**User:** "Remove the cap."

```bash
npx fibx@latest policy clear base.maxValue
```

## Error Handling

| Error / situation                     | Action                                                                                  |
| ------------------------------------- | --------------------------------------------------------------------------------------- |
| `POLICY_BLOCKED` on a trade or send   | Show the rule and limit it names. Ask whether the user wants to change the rule.         |
| Policy file unreadable                | Show the path. Ask before `policy clear`; a hand-edited file is the usual cause.         |
| `Invalid value`                       | `maxValue` is a decimal in native units; lists are comma-separated; `expiry` is ISO-8601. |
| No policy on a private-key session    | Recommend a `maxValue` (rule 3) before any transaction.                                 |

## Related Skills

- `authenticate-wallet` — which signing path is active decides how much this file matters.
- `trade` / `send` — the operations this policy bounds.
