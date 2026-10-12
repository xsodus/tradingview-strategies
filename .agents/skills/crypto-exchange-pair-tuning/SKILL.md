---
name: crypto-exchange-pair-tuning
description: Discover spot pairs on a user-selected crypto exchange and tune a user-selected strategy from this repository with consistent backtests. Use when finding or comparing crypto market pairs; does not create alerts.
metadata:
  short-description: Find pairs and tune a selected strategy
---

# Crypto Exchange Pair Tuning

Use this skill to discover eligible spot pairs on the exchange the user selects, then compare them using the repository strategy selected by the user. Finish with a candidate ledger. This skill does not create or edit alerts.

## Prepare TradingView for your agent

Before chart inspection or backtesting, identify the agent environment and use its supported browser controls:

1. **Codex:** Use the Codex Computer Use plugin. Inspect the current UI (for example, with `await cua.getState()` in CUA REPL), then open TradingView in the desktop app or in a browser. The web version is sufficient; installing the desktop app is optional.
2. **Claude:** Use `claude-in-chrome` and open TradingView in the web browser. Do not require the desktop app for this path.
3. Sign in to TradingView, then verify that the selected Computer Use tool can inspect and control the TradingView UI before proceeding with chart work.

If the correct Computer Use tool is unavailable or TradingView cannot be opened and controlled, stop and tell the user what is missing. Ask them to open TradingView in the appropriate app or browser and enable the required Computer Use tool. Do not claim a backtest was performed without inspecting TradingView results.

## Choose the exchange and market

- If the user has not named an exchange, ask which exchange to use before researching pairs. Do not assume Bitkub.
- Confirm whether they want spot or another market type and the desired quote currency if those details are not clear.
- Verify candidate symbols against the selected exchange's current market listing, using its public market data or another authoritative source. Record the exchange's exact market symbol and the matching TradingView ticker. Do not treat similarly named markets on different exchanges as interchangeable.
- Select liquid, established markets with sufficient history. Use chart appearance only as a screening aid, not as proof of future performance.
- If the selected exchange or pair is unavailable in TradingView, say so and identify what data source or workflow is missing rather than silently substituting another venue.

## Strategy and backtest setup

- If the user has not named a strategy, ask which repository strategy to tune and wait for their choice. Current options are `xsodus-supertrend-strategy.pine` (Xsodus Supertrend Strategy (Long Only) V3), `xsodus-rsi-strategy.pine` (Xsodus RSI Strategy V1), `xsodus-cdc-strategy.pine` (Xsodus CDC ActionZone V3), and `xsodus-logistic-regression.pine` (Xsodus Logistic Long Regression Strategy v.3). Do not assume Supertrend or combine results across different strategies.
- If the user has not specified a chart timeframe, ask which timeframe to use and wait for their choice before starting any backtests. Do not silently assume 4h or another timeframe.
- Read the selected Pine file before starting. Confirm its current title, Pine version, inputs, entry/exit behavior, and any built-in date filters. Use the exact selected script throughout the comparison.
- Use TradingView desktop through Computer Use to inspect charts and Strategy Tester results when no supported TradingView API is available. Observe the interface after each state-changing action.
- Use the timeframe selected by the user for every candidate. Unless the user requests otherwise, use a rolling two-year date window ending on the current date. Set the strategy's Start Date and End Date inputs explicitly; the chart's visible range does not set the strategy test window.
- If the selected script has no date inputs, use an available Strategy Tester date filter or report the actual tested coverage; do not imply a two-year test if the UI or available history does not support it.
- Confirm that TradingView history covers the requested window for the exact exchange ticker. If history is shorter, report the coverage and keep it separate from full-window results unless the user accepts the limitation.
- Keep commission, slippage, capital, and sizing assumptions consistent across candidates. Use realistic costs for the selected exchange when known, and record any changes from the script defaults.
  - Before tuning, verify the selected market's current trading fee from an authoritative exchange source. Record the fee per execution (including whether it applies to both entry and exit), then set the strategy's broker-emulator commission to that rate. Do not carry forward a remembered or prior-run fee without rechecking it. If the fee assumption changes after tuning, rerun every affected pair.

## Tune and compare

1. For each exchange-listed candidate, test reasonable values for the tunable inputs exposed by the selected script. Keep a record of each tested combination and avoid changing unrelated assumptions between runs.
2. Compare the same user-selected timeframe, date window, selected strategy version, and cost assumptions for every candidate. Record the timeframe, strategy, exchange, exact market symbol, TradingView ticker, all input values, dates, profit factor (PF), maximum drawdown (DD), trade count, and history coverage.
3. Use **PF ≥ 1.5** and **maximum DD ≤ 20%** as this project's default qualification criteria unless the user specifies different thresholds. Never round a failing result into a pass.
4. Prefer PF between 1.5 and 2 when candidates are otherwise comparable; a higher PF also qualifies. Do not choose a parameter combination solely because it fits a small number of trades. Report the tested range and any small-sample or overfitting caveat.
5. Historical results are measurements of past data, not a guarantee of future performance.

## Deliverable

Return a candidate ledger with selected timeframe, strategy and version, exchange, pair, exact market symbol and TradingView ticker, tested inputs, test dates, PF, maximum DD, trade count, history coverage, qualification status, and notes. Clearly distinguish verified listings and tested results from unverified or untested candidates.
