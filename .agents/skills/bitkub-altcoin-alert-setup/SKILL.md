---
name: bitkub-altcoin-alert-setup
description: Set up or refresh TradingView alerts for qualified Bitkub pairs after tuning parameters and asking the user about alert count and notification preferences.
metadata:
  short-description: Configure Bitkub TradingView alerts
---

# Bitkub Alert Setup

Use this skill only when the user asks to create, edit, or refresh TradingView alerts for Bitkub pairs.

## TradingView and Computer Use preflight

Before inspecting, creating, or editing alerts, confirm Computer Use is available. Start with its state inventory (for example, `await cua.getState()` in CUA REPL) and verify that the browser or desktop UI can be inspected and controlled. Use an accessible, signed-in TradingView desktop app or browser tab if one is already open. The TradingView web version is sufficient; installing the desktop app is optional.

If neither the app nor web version is accessible, or Computer Use is unavailable, stop before any alert actions and tell the user what is missing. Ask them to open and sign in to TradingView in a browser, or install and open the desktop app if they prefer, and make Computer Use available. Resume only after the UI can be observed and controlled. Do not report an alert as active unless it was verified in TradingView.

## Required tuning and user input

1. Read and follow `../crypto-exchange-pair-tuning/SKILL.md` first, selecting Bitkub spot markets for this Bitkub-only alert workflow. Produce and show the candidate ledger, including the exact tested V3 parameters, before alert setup. Do not retune from memory or use a different strategy instance.
2. Before creating or editing any alert, ask the user and wait for their answers to all of the following:
   - **Alert count and pairs:** How many active alerts should be set up, and which qualifying pairs from the ledger should be used? Do not assume a target of 20.
   - **Duplicate delivery:** Should each signal send duplicate notifications to multiple destinations, or should each alert notify through only the selected channel(s)? Explain that one TradingView alert can often enable more than one notification method, so a second alert may not be needed just to use another channel.
   - **Notification channels:** Which should be enabled for each alert: TradingView in-app notification, email, webhook/API, or a combination? Ask which destinations should receive duplicate copies if requested.
   - **Webhook details:** If webhook/API is selected, confirm the user has configured the endpoint in TradingView and provide the expected payload format. Never ask them to paste credentials into the repository or expose a secret in chat, logs, screenshots, or reports.
3. If the user's answers leave count, selected pairs, duplicate delivery, or notification channels unclear, ask a focused follow-up and wait. Do not create or edit alerts until these choices are clear.
4. Check the account's current alert quota and whether the requested count fits. If it does not fit, explain the exact limit and ask what to change. Never purchase an upgrade or silently reduce the requested count.

## Create or refresh alerts

- Use TradingView desktop through Computer Use for alert creation and verification when no supported TradingView API is available. Observe the interface after each state-changing action.
- Create the requested number of alerts only, using only selected pairs that passed the tuning criteria. Use the exact V3 strategy instance and parameter values from the confirmed candidate ledger. Recheck the condition after changing inputs; similarly named strategy instances can have different settings.
- Set the alert interval to 4h unless the user selected another interval during setup. Set the expiration to the farthest date the account allows.
- For a user-selected Bitkub webhook, use this established JSON pattern, replacing `COIN` with the selected pair's symbol:

  ```json
  { "action":"{{strategy.order.action}}", "symbol":"COIN_THB" }
  ```

- Preserve an existing configured Bitkub webhook destination. Enable only the notification methods the user selected. Never copy webhook URLs, API keys, or credentials into this repository or final response.
- If the user requests duplicate delivery to multiple channels, prefer enabling those channels on the same alert where TradingView supports it. Create duplicate alert records only when the user specifically requests them or the platform requires separate records, and make the duplicate count explicit before saving.
- After saving, verify each requested alert is active and has the intended pair, interval, V3 instance and parameters, expiration, payload (if applicable), and notification methods. If creation fails, capture the actual TradingView error and do not report the alert as active.
- Do not execute trades, place orders, or alter exchange positions.

## Report

Report how many alerts were requested, how many are active, which pairs and exact tested settings they use, and the notification methods enabled. State whether duplicate delivery was configured. Identify any quota or platform blocker. Never describe an unverified, unsaved, or expired alert as active.
