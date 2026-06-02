# Risk Checks

Looping WELL is leveraged exposure. Treat every loop as liquidation-sensitive.

## Preflight

- Confirm the user understands stkWELL staking can carry Safety Module shortfall/slashing risk.
- Confirm the market is the Base `stkWELL/USDC` isolated market.
- Confirm current stkWELL price, USDC liquidity, utilization, borrow APY, and LLTV.
- Confirm available WELL, stkWELL, USDC, existing Morpho collateral, existing USDC debt, and pending transactions.
- Confirm Bankr swap route liquidity for USDC to WELL and WELL or stkWELL to USDC.

## Borrow Sizing

Use the lower of:

- 40% of current collateral value, minus existing USDC debt.
- The maximum borrow that keeps the position comfortably below the market LLTV.
- The amount that still leaves the user-requested USDC buffer untouched.
- The amount supported by current market liquidity and swap slippage.

Do not borrow if the new debt would leave credit remaining below a conservative buffer. If no user buffer is supplied, target materially below liquidation risk and explain the chosen buffer.

## Loop Stop Conditions

Stop looping when any condition is true:

- Wallet USDC balance is less than or equal to $100 after the latest borrow/swap cycle.
- The next 40% borrow would be too small to justify gas and slippage.
- Market liquidity, slippage, utilization, price movement, or risk checks fail.
- A previous transaction has not confirmed cleanly.

## Monitoring After Loop

After each loop, report:

- Total stkWELL collateral.
- Total USDC debt.
- Remaining wallet USDC.
- Current credit remaining or health metric for the isolated market.
- Liquidation sensitivity in plain language.

If credit remaining is deteriorating, stop and recommend partial unwind.
