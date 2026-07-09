---
name: open-tv
description: Launch the local TradingView desktop app from Codex. Use when the user asks to open TV, open TradingView, open my TV, start TradingView, or launch a TradingView chart without needing indicator setup.
---

# OPEN_TV

## Quick Workflow

1. Use this skill only for opening the local TradingView desktop app or TradingView protocol links.
2. Start with the generic app URI unless the user gives a specific TradingView protocol URI:

```powershell
Start-Process 'tradingview://'
```

3. If the user provides a specific TradingView protocol URI, open that exact URI:

```powershell
Start-Process 'tradingview://chart/?symbol=BINANCE%3ABTCUSDT&interval=15'
```

4. Confirm briefly that TradingView was opened.

## Boundaries

- Do not place trades, connect brokers, change account settings, or modify chart layouts unless the user explicitly asks.
- If the local app does not open, tell the user TradingView desktop may not be installed or registered for the `tradingview://` protocol.
- If the task expands into Pine Script, SMC analysis, indicators, or chart annotation, use a more specific TradingView skill when available.
