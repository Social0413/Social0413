---
name: close-tv
description: Close the local TradingView desktop app from Codex. Use when the user asks to close TV, close TradingView, shut down TradingView, stop TradingView, or quit the TradingView desktop app.
---

# CLOSE_TV

## Quick Workflow

1. Use this skill only for closing the local TradingView desktop app.
2. Find TradingView processes by process name or window title:

```powershell
Get-Process | Where-Object { $_.ProcessName -match 'TradingView' -or $_.MainWindowTitle -match 'TradingView' }
```

3. Stop the matching processes:

```powershell
Get-Process | Where-Object { $_.ProcessName -match 'TradingView' -or $_.MainWindowTitle -match 'TradingView' } | Stop-Process -Force
```

4. Check again and confirm whether TradingView is closed.

## Boundaries

- Do not close unrelated applications.
- If stopping a process returns access denied, report that TradingView may have privileged background processes still running.
- Do not place trades, connect brokers, change account settings, or modify chart layouts.
