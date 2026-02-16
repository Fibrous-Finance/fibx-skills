---
name: aave
description: Manage DeFi positions (Supply, Borrow, Repay, Withdraw) on Aave V3. Currently supports Base network only.
license: MIT
compatibility: Requires Node.js and npx. Works with fibx CLI v0.2.6+.
metadata:
    version: 0.2.6
    author: ahmetenesdur
    category: defi-management
allowed-tools:
    - Bash(npx fibx@latest aave *)
    - Bash(npx fibx@latest status)
    - Bash(npx fibx@latest balance *)
---

# Aave V3 Management

Interact with the Aave V3 lending protocol on the **Base** network. Supply assets to earn yield or borrow assets against collateral.

## Hard Rules (CRITICAL)

1.  **Network Restriction**:
    - This skill **ONLY** supports **Base** network.
    - **NEVER** use for Monad, Citrea, or HyperEVM. IF requested, refuse and explain.
2.  **Safety First (Pre-Flight Checks)**:
    - **Gas Check**: Run `npx fibx@latest balance` to ensure enough ETH for gas.
    - **Health Factor**: Before ANY `borrow`, run `npx fibx@latest aave status`.
    - **Liquidation Risk**:
        - If Health Factor < **1.5**, **WARN** the user.
        - If Health Factor < **1.1**, **DO NOT PROCEED** without explicit double-confirmation.
3.  **Token Handling**:
    - Prefer standard symbols (USDC, ETH). For others, ask for contract address.

## Input Schema

The agent should extract the following parameters:

| Parameter | Type   | Description                                               | Required                                                  |
| :-------- | :----- | :-------------------------------------------------------- | :-------------------------------------------------------- |
| `action`  | string | One of: `status`, `supply`, `borrow`, `repay`, `withdraw` | Yes                                                       |
| `amount`  | number | Amount to process (e.g., `100`)                           | No (Required for `supply`, `borrow`, `repay`, `withdraw`) |
| `token`   | string | Token symbol or address                                   | No (Required for `supply`, `borrow`, `repay`, `withdraw`) |
| `network` | string | MUST be `base`                                            | No (Implied)                                              |

## Usage

```bash
npx fibx@latest aave <action> [amount] [token] [options]
```

## Actions

| Action     | Description                                          | usage Example                                                       |
| ---------- | ---------------------------------------------------- | ------------------------------------------------------------------- |
| `status`   | Check Account Data (Health Factor, LTV, Net Worth)   | `npx fibx@latest aave status`                                       |
| `supply`   | Deposit assets to earn yield                         | `npx fibx@latest aave supply 100 USDC`                              |
| `borrow`   | Borrow assets (requires collateral)                  | `npx fibx@latest aave borrow 0.5 ETH`                               |
| `repay`    | Repay a borrowed position (Use `max` to repay all)   | `npx fibx@latest aave repay 50 USDC` or `... repay max USDC`        |
| `withdraw` | Withdraw supplied assets (Use `max` to withdraw all) | `npx fibx@latest aave withdraw 100 USDC` or `... withdraw max USDC` |

## Dust Handling (IMPORTANT)

- **"max" Keyword**: When repaying or withdrawing, using the keyword `max` as the amount is **HIGHLY RECOMMENDED** if the user wants to close a position entirely.
- **Why?**: Passing `max` sends `MAX_UINT256` to the contract, ensuring that **all** accrued interest is covered and no "dust" (tiny residual balance like `0.000001`) remains.

## Examples

### Check Position Health

**User:** "How is my Aave position doing?"

**Agent Action:**

```bash
npx fibx@latest aave status
```

### Borrowing (With Safety Check)

**User:** "Borrow 0.5 ETH from Aave"

**Agent Actions:**

1.  Check status first (CRITICAL):
    ```bash
    npx fibx@latest aave status
    ```
2.  (If Health Factor > 2.0):
    ```bash
    npx fibx@latest aave borrow 0.5 ETH
    ```

## Cross-Skill Integration

- **Trade Skill**: If user lacks funds to supply, suggest `trade` skill to swap ETH/tokens first.
    - _Agent_: "You need USDC to supply. Shall I swap ETH for USDC first?"

## Error Handling

- **"Health Factor too low"**: Action blocked to prevent liquidation. Suggest repaying or supplying collateral.
- **"Insufficient collateral"**: Cannot borrow without supplying first.
