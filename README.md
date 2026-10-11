# Tradingview Strategy Collection
This my pinescript collection which can notify buy and sell signals and they can be back tested. 
It is free and no additional charging.


## How to use
1. Go to TradingView website or app then login with you account.
2. Open the chart that you want to see.
3. Copy the pine script then paste it to PineEditor section.
4. Press on `Save` button.
5. Press on `Add to chart` button.
6. You will see buy and sell signals on your chart. And it will alert to you if you create an alert with this strategy.

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
