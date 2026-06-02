# Contracts And Market Resolution

Always verify these against live sources before execution. Use them as expected values, not blind truth.

## Base Tokens

- WELL: `0xA88594D404727625A9437C3f886C7643872296AE`
- stkWELL: `0xe66E3A37C3274Ac24FE8590f7D84A2427194DC17`
- USDC: `0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913`

## Moonwell And Morpho

- Morpho Blue / Moonwell Morpho Markets Router on Base: `0xBBBBBbbBBb9cC5e90e3b3Af64bdAF62C37EEFFCb`
- Moonwell Morpho Base Bundler: `0x6BFd8137e702540E7A42B74178A4a49Ba43920C4`
- Morpho Bundler: `0xb98c948CFA24072e58935BC004a8A7b376AE746A`
- Morpho Views: `0xc72fCC9793a10b9c363EeaAcaAbe422E0672B42B`
- stkWELL/USDC Morpho market id: `0xb3920b96dec75b6a1144b71f963f30236fb200f3e33e93c2e9c0d222c1fa53c2`
- Market page: `https://app.morpho.org/base/market/0xb3920b96dec75b6a1144b71f963f30236fb200f3e33e93c2e9c0d222c1fa53c2/stkwell-usdc`

## Required Morpho Actions

Resolve the full `MarketParams` for the market id before calling Morpho. The fields are:

- `loanToken`: USDC
- `collateralToken`: stkWELL
- `oracle`
- `irm`
- `lltv`

Use Morpho SDK or live market API when available. If interacting directly with the Morpho contract, the action sequence is:

1. Approve stkWELL to Morpho.
2. `supplyCollateral(marketParams, collateralAmount, userAddress, "0x")`
3. `borrow(marketParams, borrowAssets, 0, userAddress, userAddress)`
4. Approve USDC to Morpho for repayment.
5. For full repayment, read borrow shares and call `repay(marketParams, 0, borrowShares, userAddress, "0x")`.
6. `withdrawCollateral(marketParams, collateralAmount, userAddress, userAddress)`

Use Bankr raw transaction submission only for verified calldata to these known targets.
