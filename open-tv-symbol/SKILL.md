---
name: open-tv-symbol
description: Open TradingView directly to a requested symbol and timeframe using a TradingView chart URL. Use when the user asks to open TV, TradingView, or a chart for a symbol such as ETH, BTC, NQ, XAUUSD, or a full exchange-prefixed ticker with a timeframe such as 15m, H1, H4, D, or W.
---

# Open TV Symbol

## Overview

Open TradingView to a specific chart by converting the user's symbol and timeframe into a TradingView URL. This skill only opens charts; it does not place trades or modify layouts.

## Quick Workflow

1. Parse the requested symbol.
2. Parse the requested timeframe.
3. Build the primary TradingView desktop protocol URL:

```text
tradingview://chart/?symbol=<URL_ENCODED_SYMBOL>&interval=<INTERVAL>
```

4. Open the primary URL with PowerShell:

```powershell
Start-Process 'tradingview://chart/?symbol=BINANCE%3AETHUSDT&interval=240'
```

5. If Windows returns `Access is denied` or TradingView opens without switching the chart, use the browser chart URL fallback:

```powershell
Start-Process 'https://www.tradingview.com/chart/?symbol=BINANCE%3AETHUSDT&interval=240'
```

6. Confirm TradingView or the browser is running:

```powershell
Get-Process | Where-Object { $_.ProcessName -match 'TradingView|chrome|msedge|firefox' -or $_.MainWindowTitle -match 'TradingView|ETHUSDT|ETH' }
```

## Symbol Rules

- If the user gives a full TradingView symbol with an exchange prefix, preserve it. Example: `BINANCE:ETHUSDT`, `NASDAQ:TSLA`, `OANDA:XAUUSD`.
- If the user gives a common crypto base symbol without an exchange, default to Binance USDT symbol text: `BINANCE:<BASE>USDT`. Example: `ETH` -> `BINANCE:ETHUSDT`.
- If the user gives a common US stock ticker without an exchange, ask for the exchange only when ambiguity matters.
- URL-encode the colon in exchange-prefixed symbols as `%3A`.

## Timeframe Rules

Use TradingView interval values:

- `1m` -> `1`
- `3m` -> `3`
- `5m` -> `5`
- `15m` -> `15`
- `30m` -> `30`
- `H1`, `1H`, `60m` -> `60`
- `H2`, `2H` -> `120`
- `H4`, `4H` -> `240`
- `D`, `1D` -> `D`
- `W`, `1W` -> `W`
- `M`, `1M` -> `M`

## Examples

- `ETH H4` -> `tradingview://chart/?symbol=BINANCE%3AETHUSDT&interval=240`
- `ETH H4` fallback -> `https://www.tradingview.com/chart/?symbol=BINANCE%3AETHUSDT&interval=240`
- `BTC 15m` -> `tradingview://chart/?symbol=BINANCE%3ABTCUSDT&interval=15`
- `BINANCE:SOLUSDT H1` -> `tradingview://chart/?symbol=BINANCE%3ASOLUSDT&interval=60`

## Boundaries

- Do not place trades.
- Do not connect brokers.
- Do not alter chart drawings, indicators, alerts, layouts, or account settings unless the user explicitly asks.
- If TradingView does not open, report that the desktop app may not be installed or registered for the `tradingview://` protocol.
