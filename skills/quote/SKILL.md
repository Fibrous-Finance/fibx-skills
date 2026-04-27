---
name: quote
description: Get a swap price quote without authentication. Check exchange rates, output amounts, and route info for any token pair on Base, Citrea, HyperEVM, or Monad. No wallet or session required.
license: MIT
compatibility: Requires Node.js 18+ and npx. Uses `npx fibx@latest`.
metadata:
    version: 0.7.0
    author: ahmetenesdur
    category: read-only
allowed-tools:
    - Bash(npx fibx@latest quote *)
---

# Quote / Price Check

Get a swap price quote for any token pair without authentication. Use this to check exchange rates, expected output amounts, and routing info before committing to a trade.

> **No wallet or session required.** This is the fastest way to explore what FibX can do.

## Prerequisites

- None. This skill works without authentication.

## Rules

1. This skill does NOT require authentication. Do NOT run `status` or `balance` before quoting.
2. If the user specifies a chain, you MUST include `--chain <name>`. If not specified, default to `base` and state it.
3. Default slippage is **0.5%**. Only include `--slippage` if the user explicitly requests a different value.
4. If the user wants to execute the swap after seeing the quote, transition to the `trade` skill.
5. For Wrap/Unwrap pairs (e.g. ETH → WETH), the quote will show a 1:1 rate — this is expected.

## Chain Reference

| Chain    | Flag               | Native Token |
| -------- | ------------------ | ------------ |
| Base     | `--chain base`     | ETH          |
| Citrea   | `--chain citrea`   | cBTC         |
| HyperEVM | `--chain hyperevm` | HYPE         |
| Monad    | `--chain monad`    | MON          |

## Commands

```bash
npx fibx@latest quote <amount> <from_token> <to_token> [--chain <chain>] [--slippage <n>] [--json]
```

## Parameters

| Parameter    | Type   | Description                              | Required |
| ------------ | ------ | ---------------------------------------- | -------- |
| `amount`     | number | Amount to quote                          | Yes      |
| `from_token` | string | Source token symbol (e.g. `ETH`, `USDC`) | Yes      |
| `to_token`   | string | Target token symbol (e.g. `USDC`, `DAI`) | Yes      |
| `chain`      | string | `base`, `citrea`, `hyperevm`, or `monad` | No       |
| `slippage`   | number | Slippage tolerance in % (e.g. `1.0`)     | No       |
| `json`       | flag   | Output as JSON                           | No       |

Default chain: `base`. Default slippage: `0.5`.

## Examples

**User:** "How much USDC would I get for 0.1 ETH?"

```bash
npx fibx@latest quote 0.1 ETH USDC
```

**User:** "Check ETH price in DAI on Monad"

```bash
npx fibx@latest quote 1 ETH DAI --chain monad
```

**User:** "Compare rates for 100 USDC to WETH"

```bash
npx fibx@latest quote 100 USDC WETH
```

**User:** "Get me a JSON quote for scripting"

```bash
npx fibx@latest quote 0.5 ETH USDC --json
```

**User:** "What's the wrap rate for ETH?"

```bash
npx fibx@latest quote 1 ETH WETH
```

> Output: `1 ETH = 1 WETH` — Wrap (direct contract call)

## Error Handling

| Error                | Action                                                       |
| -------------------- | ------------------------------------------------------------ |
| `Token not found`    | The symbol is not supported on the specified chain.          |
| `No route found`     | Liquidity may be too low or pair doesn't exist on the chain. |
| `Invalid amount`     | Amount must be a positive number.                            |
| `Rate limit / 429`   | Use `config` skill to set a custom RPC.                      |

## Related Skills

- Use `trade` to execute the swap after checking the quote.
- Use `balance` to check if you have enough tokens before trading.
- Use `config` to set a custom RPC if you encounter rate limit errors.
