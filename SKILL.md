---
name: bankr-moonwell-looper
description: Loop and unwind Moonwell WELL on Base with Bankr.bot by staking WELL to stkWELL, using stkWELL as collateral in the Moonwell/Morpho stkWELL-USDC isolated market, borrowing USDC at a conservative 40% of collateral value, swapping USDC back to WELL, repeating until only about $100 USDC remains, and reversing the full loop on request.
---

# Bankr Moonwell Looper

Use this skill when a user asks to loop, leverage, compound, deleverage, or unwind Moonwell WELL/stkWELL on Base using Bankr.bot.

This is a live DeFi workflow. Never assume prices, balances, market liquidity, borrow capacity, or contract addresses are still current. Verify every execution from live Bankr/Moonwell/Morpho state before signing or submitting transactions.

## Core Rules

- Use Base only unless the user explicitly asks otherwise.
- Use native Base WELL, not Wormhole WELL.
- Stake WELL into stkWELL before supplying collateral.
- Use the Moonwell/Morpho `stkWELL/USDC` isolated market: stkWELL is collateral, USDC is borrowed.
- Borrow at most 40% of the current stkWELL collateral value per loop.
- Stop opening new loops when the wallet's uncommitted USDC balance is less than or equal to $100, or when the next borrow would leave less than the user's requested safety buffer.
- Prefer Bankr natural-language swaps for `USDC -> WELL` and `WELL or stkWELL -> USDC`.
- Before any irreversible transaction, summarize the next action, expected amounts, resulting debt, collateral, and liquidation/credit risk in plain language.
- For unwind requests, execute the full reversal as one workflow without asking the user to separately request each step.

## References

Read `references/workflows.md` before performing a loop or unwind.
Read `references/contracts.md` when resolving contract addresses, Morpho market parameters, or transaction targets.
Read `references/risk-checks.md` before calculating borrow amounts or deciding whether to continue.

## User Commands

Treat these as triggers:

- "Loop my WELL"
- "Loop WELL until USDC is $100"
- "Borrow 40% against stkWELL and buy more WELL"
- "Unwind my Moonwell loop"
- "Close the stkWELL/USDC loop"
- "Repay and withdraw my stkWELL"

If the user gives no initial amount, use the available unstaked WELL balance after reserving any amount they ask to keep untouched.

## Execution Surface

Inside Bankr, use the wallet, swap, and arbitrary contract capabilities available to the Bankr agent. Outside Bankr, use the Bankr Agent API/CLI for natural-language swap prompts and the Bankr Wallet API to submit prepared raw EVM transactions only after all transaction targets are verified.

Use the Moonwell HTTP skill/API for Core Moonwell positions and health, but use Morpho/Moonwell isolated-market state for the stkWELL/USDC market because isolated markets are Morpho-based.
