# fibx Agentic Wallet Skills

[Agent Skills](https://agentskills.io) for the `fibx` CLI wallet, modeled after [Coinbase Agentic Wallet Skills](https://github.com/coinbase/agentic-wallet-skills). These skills enable AI agents to authenticate, check balances, send funds, and trade tokens on Base using the [`fibx`](../README.md) CLI.

## Available Skills

| Skill                                                        | Description                                          |
| ------------------------------------------------------------ | ---------------------------------------------------- |
| [authenticate-wallet](./skills/authenticate-wallet/SKILL.md) | Sign in to the wallet via email OTP (2-step process) |
| [balance](./skills/balance/SKILL.md)                         | Check ETH and token balances on Base                 |
| [send](./skills/send/SKILL.md)                               | Send ETH or ERC-20 tokens to another address         |
| [trade](./skills/trade/SKILL.md)                             | Swap/trade tokens on Base using Fibrous aggregation  |

## Installation

These skills are designed to be used with an agentic framework that can execute shell commands. Ensure the `fibx` CLI is installed and available in the environment.

1.  **Install `fibx` CLI**:
    Follow the instructions in the [main README](../README.md) to install and configure `fibx`.
    Ensure you can run `pnpm dev` or `fibx` (if aliased) from the command line.

2.  **Add Skills**:
    Import the skills from the `./skills` directory into your agent's skill registry.

## Configuration

The skills require the following environment variables to be set in the agent's runtime environment:

```bash
export PRIVY_APP_ID="your-app-id"
export PRIVY_APP_SECRET="your-app-secret"
```

## Usage

Once installed, the agent will detect relevant user intents and invoke the appropriate `fibx` commands.

**Examples:**

- "Log me in with user@example.com" -> Triggers `authenticate-wallet` skill
- "Check my balance" -> Triggers `balance` skill
- "Send 10 USDC to 0x123..." -> Triggers `send-usdc` skill
- "Swap 0.05 ETH to USDC" -> Triggers `trade` skill

## Support

For issues with the underlying wallet functionality, please refer to the main `fibx` repository.

## License

MIT
