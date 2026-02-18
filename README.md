# fibx Skills

[Agent Skills](https://agentskills.io) for the [`fibx`](https://www.npmjs.com/package/fibx) CLI. These skills enable AI agents to securely authenticate, check balances, send funds, trade tokens, and manage Aave V3 positions on **Base, Citrea, HyperEVM, and Monad**.

## Available Skills

| Skill                                                        | Description                                       | Category |
| ------------------------------------------------------------ | ------------------------------------------------- | -------- |
| [authenticate-wallet](./skills/authenticate-wallet/SKILL.md) | Email OTP login, private key import, session mgmt | Auth     |
| [balance](./skills/balance/SKILL.md)                         | Check native and ERC-20 token balances            | Wallet   |
| [send](./skills/send/SKILL.md)                               | Send native or ERC-20 tokens to an address        | Tx       |
| [trade](./skills/trade/SKILL.md)                             | Swap tokens via Fibrous aggregation               | Tx       |
| [aave](./skills/aave/SKILL.md)                               | Aave V3: supply, borrow, repay, withdraw (Base)   | DeFi     |
| [tx-status](./skills/tx-status/SKILL.md)                     | Check transaction status and explorer link        | Utility  |

## Installation

Install with [Vercel's Skills CLI](https://skills.sh):

```bash
npx skills add Fibrous-Finance/fibx-skills
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

| User Prompt                       | Skill Triggered       |
| --------------------------------- | --------------------- |
| "Log me in with user@example.com" | `authenticate-wallet` |
| "Import my private key"           | `authenticate-wallet` |
| "Log me out"                      | `authenticate-wallet` |
| "Check my balance"                | `balance`             |
| "Send 10 USDC to 0x123..."        | `send`                |
| "Swap 0.05 ETH to USDC"           | `trade`               |
| "Supply 100 USDC to Aave"         | `aave`                |
| "How is my Aave position?"        | `aave`                |
| "Did my transaction go through?"  | `tx-status`           |

## Typical Workflow

1. **Authenticate** — `authenticate-wallet` (required first)
2. **Check funds** — `balance`
3. **Execute** — `send`, `trade`, or `aave`
4. **Verify** — `tx-status`

## License

MIT
