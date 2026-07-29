# fibx Skills

[Agent Skills](https://agentskills.io) for the [`fibx`](https://www.npmjs.com/package/fibx) CLI. These skills enable AI agents to securely authenticate, check balances, send funds, trade tokens, and manage Aave V3 positions on **Base, Citrea, HyperEVM, and Monad**.

## Available Skills

| Skill                                                        | Description                                                               | Category  |
| ------------------------------------------------------------ | ------------------------------------------------------------------------- | --------- |
| [quote](./skills/quote/SKILL.md)                             | Get swap price quotes without authentication (no wallet needed)           | Read-Only |
| [authenticate-wallet](./skills/authenticate-wallet/SKILL.md) | Email OTP login, private key import (AES-256-GCM encrypted), session mgmt | Auth      |
| [wallet-info](./skills/wallet-info/SKILL.md)                 | Show active wallet address, wallet ID, and session type                   | Wallet    |
| [balance](./skills/balance/SKILL.md)                         | Check native and ERC-20 token balances                                    | Wallet    |
| [portfolio](./skills/portfolio/SKILL.md)                     | Cross-chain portfolio with USD values and DeFi positions                  | Wallet    |
| [send](./skills/send/SKILL.md)                               | Send native or ERC-20 tokens (supports `--simulate`)                      | Tx        |
| [trade](./skills/trade/SKILL.md)                             | Swap tokens via Fibrous aggregation (supports `--simulate`)               | Tx        |
| [aave](./skills/aave/SKILL.md)                               | Aave V3: status, markets, supply, borrow, repay, withdraw (`--simulate`)  | DeFi      |
| [tx-status](./skills/tx-status/SKILL.md)                     | Check transaction status and explorer link                                | Utility   |
| [config](./skills/config/SKILL.md)                           | Set custom RPC URLs (hot-reloaded, no restart needed)                     | Utility   |

## Installation

Install with [Vercel's Skills CLI](https://skills.sh):

```bash
npx skills add Fibrous-Finance/fibx-skills
```

Or clone directly into your project's skills directory:

```bash
git clone https://github.com/Fibrous-Finance/fibx-skills.git .skills/fibx-skills
```

## Getting Started

1. Install `Node.js` (v18+) and `npm`.
2. No installation of `fibx` is needed — all skills use `npx fibx@latest`.
3. Import the skills from the `./skills` directory into your agent's skill registry.

## Supported Chains

| Chain    | Native Token | Aave V3 |
| -------- | ------------ | ------- |
| Base     | ETH          | Yes     |
| Citrea   | cBTC         | No      |
| HyperEVM | HYPE         | No      |
| Monad    | MON          | No      |

## Trigger Examples

| User Prompt                         | Skill Triggered       |
| ----------------------------------- | --------------------- |
| "How much USDC for 0.1 ETH?"        | `quote`               |
| "Check ETH price"                   | `quote`               |
| "What's the swap rate?"             | `quote`               |
| "Log me in with user@example.com"   | `authenticate-wallet` |
| "Import my private key"             | `authenticate-wallet` |
| "Log me out"                        | `authenticate-wallet` |
| "What's my wallet address?"         | `wallet-info`         |
| "Which wallet am I using?"          | `wallet-info`         |
| "Check my balance"                  | `balance`             |
| "Show me my portfolio"              | `portfolio`           |
| "What's my net worth?"              | `portfolio`           |
| "Send 10 USDC to 0x123..."          | `send`                |
| "Simulate sending 0.1 ETH to 0x..." | `send`                |
| "Swap 0.05 ETH to USDC"             | `trade`               |
| "How much gas would swapping cost?" | `trade`               |
| "Supply 1 ETH to Aave"              | `aave`                |
| "What markets are on Aave?"         | `aave`                |
| "Repay my ETH debt"                 | `aave`                |
| "Did my transaction go through?"    | `tx-status`           |
| "I'm getting rate limited"          | `config`              |

## Typical Workflow

1. **Explore prices** — `quote` (no auth needed)
2. **Authenticate** — `authenticate-wallet` (required for transactions)
3. **Check funds** — `balance` or `portfolio`
4. **Simulate** — `send --simulate` or `trade --simulate` (optional)
5. **Execute** — `send`, `trade`, or `aave`
6. **Verify** — `tx-status`

## Skill Format

Each skill is a `SKILL.md` file with:

- **YAML frontmatter**: `name`, `description`, `license`, `compatibility`, `metadata` (version, author, category), and `allowed-tools` (whitelisted CLI commands)
- **Sections**: Prerequisites, Rules, Commands, Parameters, Examples, Error Handling, and Related Skills

`allowed-tools` is the safety boundary: a skill can only invoke the exact
`npx fibx@latest ...` commands it declares, so an agent cannot be talked into
running something the skill never advertised.

## Skills or MCP?

Both work; they solve different halves of the problem.

- **These skills** teach an agent _how to think_ about FibX — which command to
  reach for, what to confirm, how to read an error. They shell out to
  `npx fibx@latest` and need nothing installed.
- **The [MCP server](https://github.com/ahmetenesdur/fibx#mcp-server)** gives an
  agent _typed tools to act with_ — 11 tools with schemas, structured errors,
  and destructive-action annotations that make editors prompt for confirmation.

Use skills for prompt-driven agents (Claude Code, Cursor). Use MCP when the
client supports it. Using both together is fine — the skills' guidance applies
equally to the MCP tools.

## Related

- [fibx](https://github.com/ahmetenesdur/fibx) — the CLI and MCP server these skills drive
- [fibx-server](https://github.com/ahmetenesdur/fibx-server) — Privy wallet backend
- [fibx-telegram-bot](https://github.com/ahmetenesdur/fibx-telegram-bot) — Telegram interface
- [Fibrous Finance](https://fibrous.finance) — DEX aggregator powering swaps

## License

[MIT](https://opensource.org/licenses/MIT)
