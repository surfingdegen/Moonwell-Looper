# Workflows

## Open Or Add To A Loop

1. Resolve the user's Bankr EVM wallet on Base.
2. Check balances for WELL, stkWELL, USDC, and existing stkWELL/USDC Morpho position.
3. Verify the Base token addresses, Morpho market id, market parameters, LLTV, liquidity, borrow rate, and current stkWELL/WELL/USDC prices.
4. If the user has WELL to loop, approve and stake WELL into stkWELL through the Moonwell Safety Module/stkWELL contract. Treat staking as 1:1 unless live contract behavior proves otherwise.
5. Supply the available stkWELL collateral to the Base stkWELL/USDC Morpho isolated market.
6. Calculate the allowed USDC borrow using `references/risk-checks.md`; default target is 40% of current collateral value.
7. Borrow USDC from the same market to the user's Bankr wallet.
8. If the wallet USDC balance is greater than $100, swap only the excess USDC to WELL on Base through Bankr:

   `swap <amount over 100> USDC to WELL on base`

9. Stake the newly bought WELL into stkWELL and supply it as additional collateral.
10. Repeat borrow, swap, stake, and supply until the wallet USDC balance is about $100 or a stop condition triggers.
11. Give the final position summary and risk metrics.

## One Command Unwind

When the user asks to unwind, close the full loop in one coordinated workflow.

1. Resolve the current stkWELL collateral and USDC debt in the stkWELL/USDC Morpho market.
2. Check wallet USDC. If wallet USDC is less than debt plus accrued interest, determine the shortfall.
3. Source repayment USDC:
   - First use free wallet USDC above any user-requested reserve.
   - If more USDC is needed, sell enough free WELL for USDC through Bankr.
   - If still short, withdraw only the amount of collateral that can be safely withdrawn without breaching LLTV, unstake or swap as needed, then continue repayment. If full collateral withdrawal is blocked until repayment, explain the blocker and proceed with the largest safe partial unwind.
4. Approve USDC to Morpho and repay debt. For full close, repay by borrow shares to avoid dust.
5. Withdraw all available stkWELL collateral from the Morpho market.
6. If the user requested WELL rather than stkWELL, unstake stkWELL to WELL when the contract permits immediate withdrawal. If staking has a cooldown or restriction, report the remaining stkWELL and required next action.
7. Optionally swap WELL/stkWELL to USDC only if the user explicitly asks to end in USDC.
8. Confirm the position is closed or state the exact residual debt/collateral and why it remains.

## Failure Handling

- If a transaction fails, stop the loop and re-read balances/position before continuing.
- If Bankr cannot construct a contract call from natural language, prepare calldata with a verified ABI and submit through Bankr Wallet API.
- If the Morpho market parameters cannot be verified, do not transact.
- If swap slippage is high, reduce size or stop and report the issue.
