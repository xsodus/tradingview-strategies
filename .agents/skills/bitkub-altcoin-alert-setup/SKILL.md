---
name: bitkub-altcoin-alert-setup
description: Set up or refresh TradingView alerts for qualified Bitkub pairs after tuning parameters and asking the user about alert count and notification preferences.
metadata:
  short-description: Configure Bitkub TradingView alerts
---

# Bitkub Alert Setup

Use this skill only when the user asks to create, edit, or refresh TradingView alerts for Bitkub pairs.

## Prepare TradingView for your agent

Before inspecting, creating, or editing alerts, identify the agent environment and use its supported browser controls:

1. **Codex:** Use the Codex Computer Use plugin. Inspect the current UI (for example, with `await cua.getState()` in CUA REPL), then open TradingView in the desktop app or in a browser. The web version is sufficient; installing the desktop app is optional.
2. **Claude:** Use `claude-in-chrome` and open TradingView in the web browser. Do not require the desktop app for this path.
3. Sign in to TradingView, then verify that the selected Computer Use tool can inspect and control the TradingView UI before proceeding with alert work.

If the correct Computer Use tool is unavailable or TradingView cannot be opened and controlled, stop and tell the user what is missing. Ask them to open TradingView in the appropriate app or browser and enable the required Computer Use tool. Do not report an alert as active unless it was verified in TradingView.

## Required tuning and user input

1. Before tuning, ask the user which repository strategy and chart timeframe they want to use for the alerts. If they have not chosen a strategy, present these files and titles and wait: `xsodus-supertrend-strategy.pine` (Xsodus Supertrend Strategy (Long Only) V3), `xsodus-rsi-strategy.pine` (Xsodus RSI Strategy V1), `xsodus-cdc-strategy.pine` (Xsodus CDC ActionZone V3), or `xsodus-logistic-regression.pine` (Xsodus Logistic Long Regression Strategy v.3). If timeframe is missing, ask which timeframe to use; do not assume 4h or start tuning until both choices are clear.
2. Read and follow `../crypto-exchange-pair-tuning/SKILL.md` with the user's selected strategy and timeframe and Bitkub spot markets. Produce and show the candidate ledger, including timeframe, exact script version, and tested inputs, before alert setup. Do not retune from memory or use a different strategy instance.
3. Before creating or editing any alert, ask the user and wait for their answers to all of the following:
   - **Alert count and pairs:** How many active alerts should be set up, and which qualifying pairs from the ledger should be used? Do not assume a target of 20.
   - **Strategy and timeframe confirmation:** Show the selected strategy, version, and timeframe from the tuning ledger. Use those same choices for every requested alert; if the user changes either, rerun tuning for the new choice before proceeding.
   - **Duplicate delivery:** Should each signal send duplicate notifications to multiple destinations, or should each alert notify through only the selected channel(s)? Explain that one TradingView alert can often enable more than one notification method, so a second alert may not be needed just to use another channel.
   - **Notification channels:** Which should be enabled for each alert: TradingView in-app notification, email, webhook/API, or a combination? Ask which destinations should receive duplicate copies if requested.
   - **Webhook details:** If webhook/API is selected, confirm the user has configured the endpoint in TradingView and provide the expected payload format. Never ask them to paste credentials into the repository or expose a secret in chat, logs, screenshots, or reports.
3. If the user's answers leave count, selected pairs, duplicate delivery, or notification channels unclear, ask a focused follow-up and wait. Do not create or edit alerts until these choices are clear.
4. Check the account's current alert quota and whether the requested count fits. If it does not fit, explain the exact limit and ask what to change. Never purchase an upgrade or silently reduce the requested count.

## Create or refresh alerts

- Use TradingView desktop through Computer Use for alert creation and verification when no supported TradingView API is available. Observe the interface after each state-changing action.
- Create the requested number of alerts only, using only selected pairs that passed the tuning criteria. Use the exact selected strategy instance, version, parameter values, and timeframe from the candidate ledger. Recheck the condition after changing inputs; similarly named strategy instances can have different settings.
- Set each alert interval to the same timeframe used in the ledger. If TradingView does not allow that interval for an alert, explain the constraint and ask the user what interval to use before saving. Set the expiration to the farthest date the account allows.
- For a user-selected Bitkub webhook, use this established JSON pattern, replacing `COIN` with the selected pair's symbol:

  ```json
  { "action":"{{strategy.order.action}}", "symbol":"COIN_THB" }
  ```

- Preserve an existing configured Bitkub webhook destination. Enable only the notification methods the user selected. Never copy webhook URLs, API keys, or credentials into this repository or final response.
- If the user requests duplicate delivery to multiple channels, prefer enabling those channels on the same alert where TradingView supports it. Create duplicate alert records only when the user specifically requests them or the platform requires separate records, and make the duplicate count explicit before saving.
- After saving, verify each requested alert is active and has the intended pair, interval, selected strategy and parameters, expiration, payload (if applicable), and notification methods. If creation fails, capture the actual TradingView error and do not report the alert as active.
- Do not execute trades, place orders, or alter exchange positions.

## Report

Report the selected strategy and version, how many alerts were requested, how many are active, which pairs and exact tested settings they use, and the notification methods enabled. State whether duplicate delivery was configured. Identify any quota or platform blocker. Never describe an unverified, unsaved, or expired alert as active.
