---
name: smc-weekly-ob-fvg
description: Create and maintain a TradingView Pine Script indicator for Smart Money Concepts weekly order blocks, fair value gaps, and recent range high/low levels. Use when the user asks Codex to draw SMC, weekly OB, weekly FVG, bullish green OB zones, bearish red OB zones, stop zones at midpoint breaks, or mark the last 365 days high and low.
---

# SMC Weekly OB FVG

## Overview

Build Pine Script indicators for SMC analysis on TradingView. Version 1 focuses on the current ETHUSDT chart and draws weekly timeframe order blocks and fair value gaps while the user views lower timeframes such as H4.

## Version 1 Rules

- Use weekly (`W`) candles as the high timeframe source.
- Draw bullish order blocks in green.
- Draw bearish order blocks in red.
- Detect weekly FVG with the classic 3-candle imbalance:
  - Bullish FVG: week 3 low is above week 1 high.
  - Bearish FVG: week 3 high is below week 1 low.
  - Only keep FVG zones whose gap size is at least 3% of the gap midpoint price by default.
- Detect weekly OB with a sensitive displacement rule so zones appear reliably during testing:
  - Bullish OB: a completed weekly candle closes above the previous bearish weekly candle high; use that previous bearish candle as the OB.
  - Bearish OB: a completed weekly candle closes below the previous bullish weekly candle low; use that previous bullish candle as the OB.
- Extend each valid OB to the right.
- Stop extending a bullish OB when price breaks below its midpoint.
- Stop extending a bearish OB when price breaks above its midpoint.
- Extend each valid FVG to the right.
- Stop extending a bullish FVG when price breaks below its midpoint.
- Stop extending a bearish FVG when price breaks above its midpoint.
- Mark the highest high and lowest low from the most recent 365 daily candles with visible horizontal lines.
- Anchor each 365-day high/low line to the daily candle where that high/low happened, then extend it to the right until the level changes.
- Build weekly candle history by aggregating the current chart bars into weekly candles. Avoid relying on weekly `request.security()` history for OB/FVG drawing, because those zones must remain visible when the user switches between weekly, daily, and intraday charts.
- Include a `Show debug label` input. If the chart appears blank, enable it and inspect completed weekly candles, OB count, and FVG count before changing the detection rules.

## Asset

Use [assets/smc_weekly_ob_fvg_v1.pine](assets/smc_weekly_ob_fvg_v1.pine) as the first Pine Script version.

## TradingView Workflow

1. Open ETH H4 with `open-tv-symbol`:

```powershell
Start-Process 'https://www.tradingview.com/chart/?symbol=BINANCE%3AETHUSDT&interval=240'
```

2. Open TradingView Pine Editor.
3. Paste the contents of `assets/smc_weekly_ob_fvg_v1.pine`.
4. Save the script as `CODEX SMC Weekly OB FVG v1`.
5. Add it to the current ETHUSDT chart.

## Boundaries

- Do not place trades.
- Treat this as visual analysis support, not a signal system.
- Do not auto-delete existing user drawings or indicators.
- Ask before changing OB/FVG definitions, colors, invalidation rules, or target timeframe.
