# fibx Skills

[Agent Skills](https://agentskills.io) for the `fibx` CLI wallet. These skills enable AI agents (Claude, GPT, etc.) to securely authenticate, check balances, send funds, and trade tokens on **Base, Citrea, HyperEVM, and Monad** using the [`fibx`](https://www.npmjs.com/package/fibx) CLI.

## Available Skills

| Skill                                                        | Description                                                        | Category        |
| ------------------------------------------------------------ | ------------------------------------------------------------------ | --------------- |
| [authenticate-wallet](./skills/authenticate-wallet/SKILL.md) | Sign in via email OTP or Private Key Import                        | Auth            |
| [aave](./skills/aave/SKILL.md)                               | Manage Aave V3 positions (Supply, Borrow, Repay, Withdraw) on Base | DeFi Management |
| [balance](./skills/balance/SKILL.md)                         | Check native (ETH, MON, etc.) and token balances                   | Wallet Data     |
| [send](./skills/send/SKILL.md)                               | Send ETH or ERC-20 tokens to another address                       | Transaction     |
| [trade](./skills/trade/SKILL.md)                             | Swap/trade tokens using Fibrous aggregation                        | Transaction     |
| [tx-status](./skills/tx-status/SKILL.md)                     | Check transaction status and get explorer link                     | Utility         |

## Installation

To use these skills with an agent framework:

1.  **Dependencies**:
    - `node` (v18+)
    - `npm`
    - No manual installation of `fibx` is required; skills use `npx fibx@latest`.

2.  **Add Skills**:
    Import the skills from the `./skills` directory into your agent's skill registry.

## Configuration

The skills require the following environment variables to be set in the agent's runtime environment:

```bash
export PRIVY_APP_ID="your-app-id"
export PRIVY_APP_SECRET="your-app-secret"
```

> **Note:** Private key authentication is handled via the interactive `npx fibx@latest auth import` command — no `PRIVATE_KEY` environment variable is needed.

## Examples

### Common Triggers

- "Log me in with user@example.com" → Triggers [`authenticate-wallet`](./skills/authenticate-wallet/SKILL.md)
- "Log me out" → Triggers [`authenticate-wallet`](./skills/authenticate-wallet/SKILL.md)
- "Import my private key" → Triggers [`authenticate-wallet`](./skills/authenticate-wallet/SKILL.md)
- "Check my balance" → Triggers [`balance`](./skills/balance/SKILL.md)
- "Send 10 USDC to 0x123..." → Triggers [`send`](./skills/send/SKILL.md)
- "Send 1 MON to 0x456..." → Triggers [`send`](./skills/send/SKILL.md)
- "Swap 0.05 ETH to USDC" → Triggers [`trade`](./skills/trade/SKILL.md)
- "Supply 100 USDC to Aave" → Triggers [`aave`](./skills/aave/SKILL.md)
- "Borrow 0.1 ETH from Aave" → Triggers [`aave`](./skills/aave/SKILL.md)
- "Did my transaction go through?" → Triggers [`tx-status`](./skills/tx-status/SKILL.md)

### Detailed Workflow: Token Swap

**User:** "Swap 0.05 ETH to USDC on Base"

**Agent Workflow:**

1.  **Detection**: Agent matches request to `trade` skill.
2.  **Validation**: Agent checks `Hard Rules` (Balances, Chain defaults).
3.  **Execution**:
    ```bash
    npx fibx@latest trade 0.05 ETH USDC --chain base
    ```
    _(CLI automatically simulates the swap first)_
4.  **Verification**: Agent uses `tx-status` to confirm success.

## License

MIT
