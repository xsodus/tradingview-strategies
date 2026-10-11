---
name: crypto-exchange-pair-tuning
description: Discover spot pairs on a user-selected crypto exchange and tune the repository's Supertrend strategy with consistent backtests. Use when finding or comparing crypto market pairs; does not create alerts.
metadata:
  short-description: Find and tune crypto exchange pairs
---

# Crypto Exchange Pair Tuning

Use this skill to discover eligible spot pairs on the exchange the user selects, then compare them with the repository's TradingView strategy. Finish with a candidate ledger. This skill does not create or edit alerts.

## TradingView and Computer Use preflight

Before chart inspection or backtesting, confirm Computer Use is available. Start with its state inventory (for example, `await cua.getState()` in CUA REPL) and verify that the browser or desktop UI can be inspected and controlled. Use an accessible, signed-in TradingView desktop app or browser tab if one is already open. The TradingView web version is sufficient; installing the desktop app is optional.

If neither the app nor web version is accessible, or Computer Use is unavailable, stop before any chart actions and tell the user what is missing. Ask them to open and sign in to TradingView in a browser, or install and open the desktop app if they prefer, and make Computer Use available. Resume only after the UI can be observed and controlled. Do not claim a backtest was performed without inspecting TradingView results.

## Choose the exchange and market

- If the user has not named an exchange, ask which exchange to use before researching pairs. Do not assume Bitkub.
- Confirm whether they want spot or another market type and the desired quote currency if those details are not clear.
- Verify candidate symbols against the selected exchange's current market listing, using its public market data or another authoritative source. Record the exchange's exact market symbol and the matching TradingView ticker. Do not treat similarly named markets on different exchanges as interchangeable.
- Select liquid, established markets with sufficient history. Use chart appearance only as a screening aid, not as proof of future performance.
- If the selected exchange or pair is unavailable in TradingView, say so and identify what data source or workflow is missing rather than silently substituting another venue.

## Strategy and backtest setup

- Read `xsodus-supertrend-strategy.pine` before starting. Confirm its current title, Pine version, and inputs; the current strategy is **Xsodus Supertrend Strategy (Long Only) V3**, Pine v6. Use the exact current script throughout a comparison.
- Use TradingView desktop through Computer Use to inspect charts and Strategy Tester results when no supported TradingView API is available. Observe the interface after each state-changing action.
- Unless the user requests otherwise, use 4h candles and a rolling two-year date window ending on the current date. Set the strategy's Start Date and End Date inputs explicitly; the chart's visible range does not set the strategy test window.
- Confirm that TradingView history covers the requested window for the exact exchange ticker. If history is shorter, report the coverage and keep it separate from full-window results unless the user accepts the limitation.
- Keep commission, slippage, capital, and sizing assumptions consistent across candidates. Use realistic costs for the selected exchange when known, and record any changes from the script defaults.

## Tune and compare

1. For each exchange-listed candidate, test reasonable values for the actual V3 inputs: Supertrend length and multiplier, optional EMA filter and length, stop-loss type and settings, take-profit setting and risk/reward ratio, and trailing-stop settings. Keep a record of each tested combination and avoid changing unrelated assumptions between runs.
2. Compare the same timeframe, date window, script version, and cost assumptions for every candidate. Record the exchange, exact market symbol, TradingView ticker, all input values, dates, profit factor (PF), maximum drawdown (DD), trade count, and history coverage.
3. Use **PF ≥ 1.5** and **maximum DD ≤ 20%** as this project's default qualification criteria unless the user specifies different thresholds. Never round a failing result into a pass.
4. Prefer PF between 1.5 and 2 when candidates are otherwise comparable; a higher PF also qualifies. Do not choose a parameter combination solely because it fits a small number of trades. Report the tested range and any small-sample or overfitting caveat.
5. Historical results are measurements of past data, not a guarantee of future performance.

## Deliverable

Return a candidate ledger with exchange, pair, exact market symbol and TradingView ticker, strategy version and inputs, test dates, PF, maximum DD, trade count, history coverage, qualification status, and notes. Clearly distinguish verified listings and tested results from unverified or untested candidates.
