# TradingView Strategy Collection

This repository contains Pine Script strategies for exploring entry and exit signals, viewing them on TradingView charts, and evaluating their historical performance with TradingView's Strategy Tester. The strategies are provided as source code to review, configure, and adapt. They generate chart signals and alerts; they do not connect to an exchange or place trades.

## Strategies

- [`xsodus-supertrend-strategy.pine`](xsodus-supertrend-strategy.pine) — Supertrend-based strategy with configurable long and short behavior.
- [`xsodus-rsi-strategy.pine`](xsodus-rsi-strategy.pine) — RSI entries with optional EMA trend filtering and stop loss.
- [`xsodus-cdc-strategy.pine`](xsodus-cdc-strategy.pine) — CDC ActionZone strategy.
- [`xsodus-logistic-regression.pine`](xsodus-logistic-regression.pine) — Logistic-regression-based signal strategy.


## How to use

1. Sign in to TradingView and open the chart for the market you want to study.
2. Open **Pine Editor**, choose a strategy file from this repository, and copy its full contents into the editor.
3. Save the script, then select **Add to chart**. Resolve any Pine Editor errors before continuing.
4. Open the strategy's settings and configure its inputs, chart symbol, timeframe, and backtest date range for your analysis.
5. Review the chart signals and the **Strategy Tester** results. Set realistic commission and slippage assumptions where available, and compare results across appropriate market conditions.
6. To receive notifications, create an alert from the strategy on the chart. Select the intended strategy instance and alert condition, then choose the interval, expiration, and notification methods. If using a webhook, configure its URL in TradingView and use the payload expected by your receiving service.

Keep the script version and input values consistent between backtests and alerts. Historical results are for research and do not predict future performance.

## Bitkub altcoin alert workflow

Use this process to select and maintain a set of up to 20 Bitkub altcoin strategy alerts. The detailed reusable instructions are in the [Bitkub Altcoin Alerts skill](.agents/skills/bitkub-altcoin-alerts/SKILL.md).

### Backtest and qualify pairs

1. Review `xsodus-supertrend-strategy.pine` and identify the intended strategy version before starting. Use that same version for every candidate.
2. In TradingView, select the Bitkub spot pair and set the chart to 4 hours. Set the strategy start date to two years before the test date and its end date to the current date. Confirm the pair has data for the full period; label or exclude pairs with shorter history.
3. Tune the strategy inputs per pair. Record the version, exact inputs, test dates, profit factor (PF), maximum drawdown (DD), and trade count.
4. A pair qualifies when PF is at least 1.5 and maximum DD is no more than 20%. Prefer PF between 1.5 and 2 when candidates are otherwise comparable; higher PF still qualifies. Never round a failing result into a pass.
5. Avoid relying on a small number of trades. Include the trade count and history coverage when comparing candidates. Backtest results describe historical performance and do not guarantee future results.

### Create or refresh alerts

1. Use the exact strategy instance from TradingView's condition dropdown, including the tested version and parameter values. Recheck it after changing inputs; a matching strategy name can refer to a different parameter set.
2. Set the alert interval to 4 hours and choose the farthest expiration the account allows.
3. Use this Bitkub webhook message pattern, replacing `COIN` with the pair's symbol:

   ```json
   { "action":"{{strategy.order.action}}", "symbol":"COIN_THB" }
   ```

4. Keep the existing Bitkub webhook destination and confirm Webhook URL notifications are enabled. Do not put the webhook URL or API key in this repository or documentation.
5. After saving, verify the alert is active and confirm its pair, interval, strategy version and parameters, message, and webhook setting.

### Replace a pair that no longer qualifies

If a later parameter update makes an active pair fail either PF or DD threshold, test new Bitkub pairs using the same strategy version, 4h interval, and two-year window. Replace the failing pair only after a candidate passes both thresholds. Update the existing alert in place when the 20-alert plan limit is full, then verify the replacement's alert condition, payload, webhook, and active status. If no candidate passes, report that the 20 qualifying-pair target is unmet. Do not silently remove a working alert or purchase a plan upgrade.

This workflow manages TradingView alerts only; it does not execute trades or place exchange orders.

## Node.js tooling

Install the development dependencies with `npm install`. TypeScript source files belong in `src/`; compile them with `npm run build` or check types without emitting files with `npm run typecheck`. The generated JavaScript and declarations go to `dist/`.

Install this repository's agent skills for Codex with `npm run skills:install`.
