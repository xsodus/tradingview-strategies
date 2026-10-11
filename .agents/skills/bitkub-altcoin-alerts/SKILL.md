---
name: bitkub-altcoin-alerts
description: Find and tune Bitkub crypto pairs for TradingView Supertrend V2 alerts, validate two-year 4h backtests, and create or replace up to 20 webhook alerts.
metadata:
  short-description: Tune Bitkub pairs and maintain TradingView alerts
---

# Bitkub Altcoin Alerts

Use this skill when the user asks to discover Bitkub pairs, tune the repository's TradingView strategy for them, or create, review, or refresh a set of up to 20 corresponding TradingView alerts.

## Source and operating mode

- Read `xsodus-supertrend-strategy.pine` in the `xsodus/tradingview-strategies` repository before tuning. Confirm which strategy version is the intended target and use that exact script version in TradingView.
- Use TradingView desktop through Computer Use for chart inspection, Strategy Tester results, alert condition selection, and alert verification when no supported TradingView API is available. Observe the interface after each state-changing action.
- Keep the requested chart interval at 4h unless the user changes it. Use a rolling two-year backtest window ending at the current date. Set the script's date inputs explicitly; do not confuse the chart's visible candle range with the strategy's configured test window.
- Use Bitkub spot pairs only. Confirm the symbol is listed on Bitkub and construct the exchange payload symbol as `COIN_THB`.

## Pair selection and backtesting

1. Start with liquid, established Bitkub altcoin pairs. Review recent price behavior for a consolidation/base with a plausible upward setup, but treat this only as a candidate screen, not as evidence of strategy performance.
2. Tune parameters per pair using the Strategy Tester on 4h candles and the same two-year window. Record the exact script version, all inputs, test dates, profit factor, maximum drawdown, and trade count for each candidate.
3. A pair qualifies when **profit factor is at least 1.5** and **maximum drawdown is at most 20%**. Prefer PF in the 1.5–2 range when comparing otherwise similar candidates; a higher PF also qualifies. Do not round a failing metric into a pass.
4. Reject pairs whose available history does not cover the full two-year window, or clearly label the shorter coverage and keep it out of a full-window comparison unless the user accepts that limitation.
5. Avoid selecting parameters solely to fit a handful of trades. Report trade count, history coverage, and any small-sample or overfitting caveat with the metrics. Backtests are historical measurements, not a guarantee of future results.
6. Keep a candidate ledger during the run: pair, exact V2 inputs, dates, PF, max DD, trades, qualification status, and alert status.

## Maintain the 20-alert set

- The target is 20 qualifying active Bitkub strategy alerts, subject to the user's TradingView plan limit. Existing alerts that fail the current threshold do not count as qualifying slots.
- When a parameter refresh causes an existing pair to fail either metric, search for a replacement Bitkub pair and test it with the same version, interval, and two-year dates. Replace the failing pair only after a replacement passes both thresholds. Update the existing alert in place when the plan is at its alert limit; keep the set at 20 and verify the new pair, V2 instance, parameters, and active status afterward.
- If no replacement passes, retain the failing alert only as an explicitly marked non-qualifying item; do not claim the 20-pair target is met. If TradingView's limit prevents an in-place update or requires a plan purchase, stop and report the specific blocker rather than purchasing an upgrade or silently dropping a working alert.
- Do not execute trades, place orders, or alter exchange positions. This workflow creates or edits TradingView alerts only.

## Alert setup and webhook

For every qualifying pair:

1. Create or edit a strategy alert using the **exact V2 dropdown instance with the tested parameter values**. Recheck this condition after changing the script inputs; similarly named versions or parameter sets are different alert conditions.
2. Set the alert interval to 4h and extend expiration as far as the account UI allows.
3. Use the established Bitkub JSON message pattern, substituting the pair symbol:

   ```json
   { "action":"{{strategy.order.action}}", "symbol":"COIN_THB" }
   ```

4. Preserve the user's existing configured Bitkub webhook destination and API-key query string. Verify the Webhook URL notification is enabled. Never copy the webhook URL, API key, or other credential into this skill, logs, screenshots shared outside the app, or the final response.
5. After saving, verify the alert appears active with the intended pair, 4h interval, V2 condition and parameters, JSON message, and webhook enabled. If creation fails, capture the actual TradingView error and do not report the alert as active.

## Report

Report the total active alert count and the count that currently passes both criteria. List each qualifying pair with PF, maximum DD, trade count, and tested parameters. Identify failed or limited-history pairs separately, disclose when replacements were made, and explain any alert-quota blocker. Never describe an unverified, unsaved, or expired alert as active.
