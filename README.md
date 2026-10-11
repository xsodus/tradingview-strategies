# TradingView Strategy Collection

This repository contains Pine Script strategies for exploring entry and exit signals, viewing them on TradingView charts, and evaluating their historical performance with TradingView's Strategy Tester. The strategies are provided as source code to review, configure, and adapt. They generate chart signals and alerts; they do not connect to an exchange or place trades.

## Strategies

- [`xsodus-supertrend-strategy.pine`](xsodus-supertrend-strategy.pine) — Long-only Supertrend strategy with configurable filters and risk controls.
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

## Crypto pair research and Bitkub alerts

Pair research is exchange-agnostic; the alert skill remains specific to Bitkub:

1. [Crypto Exchange Pair Tuning](.agents/skills/crypto-exchange-pair-tuning/SKILL.md) asks which exchange and market to use when unspecified, verifies listed pairs, and compares the current Supertrend strategy on consistent 4h, two-year backtests by default. Project qualification defaults are PF ≥ 1.5 and maximum DD ≤ 20%.
2. [Bitkub Alert Setup](.agents/skills/bitkub-altcoin-alert-setup/SKILL.md) uses the tuning ledger for Bitkub spot pairs, then asks the user how many alerts to set up, which pairs to use, whether to duplicate delivery, and which channels to enable (in-app, email, webhook/API) before changing alerts.

Alert setup uses the tested strategy parameters, checks the account quota, and verifies each saved alert. It does not execute trades or place exchange orders.

## Node.js tooling

Install the development dependencies with `npm install`. TypeScript source files belong in `src/`; compile them with `npm run build` or check types without emitting files with `npm run typecheck`. The generated JavaScript and declarations go to `dist/`.

## Agent skills

Install this repository's skills for Codex from the repository root:

```sh
npm install
npm run skills:install
```

Use the **Crypto Exchange Pair Tuning** skill to find and compare pairs on a selected exchange. Name the exchange in your request; if you do not, the skill asks which exchange and market to use. Use **Bitkub Alert Setup** when you want TradingView alerts for Bitkub pairs. It first uses the tuning workflow, then asks how many alerts and which pairs to set up, whether notifications should be duplicated, and which channels to enable before changing alerts.

For example, ask Codex: “Use Crypto Exchange Pair Tuning to find and backtest spot pairs on `<exchange>`.” For Bitkub alerts, ask: “Use Bitkub Alert Setup to tune Bitkub pairs and prepare alerts.”

Both skills need TradingView access and Computer Use for chart or alert actions. A signed-in TradingView web tab works; installing the desktop app is optional. Before proceeding, the skill checks that Computer Use can inspect and control the TradingView UI. If neither the app nor the web version is accessible, open and sign in to TradingView in a browser (or install and open the desktop app), then rerun the request with Computer Use available.
