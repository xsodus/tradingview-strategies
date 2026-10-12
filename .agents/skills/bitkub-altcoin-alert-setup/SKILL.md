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
   - Tune every pair independently. Keep a separate record of the tested inputs and results for each exact Bitkub symbol; do not choose one portfolio-wide "best" input set and apply it to every pair. Identical settings are fine only when they independently qualify as the selected pair's result.
   - Configure and verify Bitkub trading fees in the backtest before tuning any pair. Use the correct fee model/rates for the user's trading mode; if the fee values or model are unavailable, ask rather than tuning with zero or guessed fees. Include the applied fees with the ledger's other cost assumptions. If earlier tuning omitted fees, rerun the affected pairs with fees before using those results to manage alerts.
   - When refreshing existing alerts, inventory their exact symbols and count first. Treat an explicit request to update all existing alerts as the user's requested scope, while still listing the pairs and confirming any pairs that do not qualify.
   - When replacing underperforming pairs, first reconcile the requested replacement count with the live alert inventory and identify the exact failing symbols. Ask the user to choose/confirm replacement symbols when they are not already explicit. A replacement must qualify from its own fee-inclusive tuning row; do not transfer the removed pair's parameters or reuse another pair's result. Do not delete or replace alerts based on an assumed count or inferred pair mapping.
   - The ledger must make the pair-to-settings mapping unambiguous and include test dates, costs, sizing, PF, max DD, trade count, history coverage, and qualification status. Preserve best observed failing candidates as failures; never round or relabel them as passing.
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
- If the user selects or requests a particular script version (including V3), make sure every affected alert condition uses that version. A chart script upgrade alone does not update existing alerts: verify and save the strategy version and that pair's independently tuned inputs in each alert condition, one at a time.
- Update alerts one at a time, mapping each alert's exact symbol to that symbol's own qualified ledger row. Before saving each alert, set and recheck its strategy inputs; do not carry settings over from the previously edited alert or leave all alerts on the chart's shared/default inputs. Verify the saved alert condition individually after each save.
- Whenever tuning changes a pair's inputs, immediately update that pair's alert condition to the matching strategy version and inputs before moving to another pair. Verify the saved condition against the ledger row and retain the alert's existing notifications and message unless the user requested a change.
- If the user asks for every existing alert but some pairs do not pass, do not apply a failing setup or claim the full request is complete. Show the per-pair results and ask how to handle the unqualified pairs (for example, continue tuning or leave those alerts unchanged) before editing those records. Qualified alerts may only be edited if the user's requested count/pair selection and notification choices are clear.
- If the user chooses to remove a failing alert, delete only the exact identified alert after that choice is explicit, then verify it is absent. If replacing it, qualify the replacement first, confirm the pair mapping, then update/create its alert and verify the saved condition before treating the replacement as complete. Do not imply that deletion preserves an alert slot, billing period, or quota unless TradingView confirms it.
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
