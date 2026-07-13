---
name: smc-weekly-ob-fvg
description: Create and maintain a TradingView Pine Script indicator for Smart Money Concepts weekly order blocks, weekly fair value gaps, and daily CHOCH/MSS horizontal structure lines. Use when the user asks Codex to draw SMC, weekly OB, weekly FVG, bullish green OB zones, bearish red OB zones, stop zones at midpoint breaks, or daily structure shifts.
---

# SMC Weekly OB FVG

## Overview

Build Pine Script indicators for SMC analysis on TradingView. Version 1 focuses on the current ETHUSDT chart and draws weekly timeframe order blocks, weekly fair value gaps, and daily structure lines while the user views lower timeframes such as H4.

## Version 1 Rules

- Use weekly (`W`) candles as the high timeframe source.
- Draw bullish order blocks in green.
- Draw bearish order blocks in red.
- Draw bullish FVG in a brighter yellow.
- Draw bearish FVG in a darker yellow/olive.
- Detect weekly FVG with the classic 3-candle imbalance:
  - Bullish FVG: week 3 low is above week 1 high.
  - Bearish FVG: week 3 high is below week 1 low.
  - Only keep FVG zones whose gap size is at least 3% of the gap midpoint price by default.
- Detect weekly OB with a structure-break rule:
  - Bullish OB: a completed weekly close breaks above the recent weekly structure high; search backward for the nearest bearish weekly candle and use it as the OB.
  - Bearish OB: a completed weekly close breaks below the recent weekly structure low; search backward for the nearest bullish weekly candle and use it as the OB.
- Do not draw the same OB source candle twice. A source weekly candle plus direction can create at most one OB zone.
- Extend each valid OB to the right.
- Stop extending a bullish OB when price breaks below its midpoint.
- Stop extending a bearish OB when price breaks above its midpoint.
- Extend each valid FVG to the right.
- Stop extending a bullish FVG when price breaks below its midpoint.
- Stop extending a bearish FVG when price breaks above its midpoint.
- Always stop OB/FVG zones at midpoint breaks; do not skip invalidation for replay mode.
- Keep object count conservative by default (`Maximum zones per type` defaults to 40) because TradingView replay can stop drawing the whole indicator when object pressure is too high.
- Keep replay calculations light. Daily/weekly replay steps recalculate more frequently than monthly replay, so avoid long history loops in replay-sensitive code.
- Always show `OB` and `FVG` text inside zones.
- Use more transparent fills by default because overlapping zones become visually brighter when they stack.
- Do not draw the 365-day high/low feature. It was removed because it added resource pressure in replay mode.
- Use weekly (`W`) data for OB/FVG.
- On daily charts, run the known-working `03_H4M15`-derived structure logic directly from chart candles.
- On intraday charts, build completed daily candles from the current chart bars, then run the same daily CHOCH/MSS logic on those completed daily candles. This mirrors the weekly OB/FVG aggregation approach and avoids unstable `request.security("D")` display behavior.
- Do not rejudge CHOCH/MSS from the current intraday timeframe. H4/M15 bars are only used to reconstruct daily candles for display.
- Use green tones for bullish/long CHOCH/MSS and red tones for bearish/short CHOCH/MSS. CHOCH should be darker; MSS should be brighter.
- Use `D CHOCH` when a daily close breaks the latest CHOCH pivot in the opposite tracked trend direction.
- Use `D MSS` when a daily close breaks the latest MSS pivot in the opposite tracked trend direction and the candle body meets the ATR displacement filter.
- Do not use `plotshape` or separate `label.new` labels for daily CHOCH/MSS.
- Draw CHOCH/MSS as fixed horizontal structure-break segments from the broken pivot candle to the candle that confirms the break. Do not extend CHOCH/MSS into future candles.
- Put the `CHOCH` or `MSS` text below the structure segment so it does not cover the line or candles. Pine line objects do not support native line text, so use a transparent text box under the line instead of a separate label.
- Use darker colors for CHOCH and brighter colors for MSS.
- Keep CHOCH and MSS as separate pivot systems: CHOCH defaults to swing length 2; Daily MSS defaults to swing length 4 plus ATR body filter.
- When a bullish Daily MSS forms, freeze the latest confirmed Daily swing low as its Bias invalidation level; for a bearish Daily MSS, freeze the latest confirmed Daily swing high. A completed Daily close through that fixed level changes the Bias to Neutral. Do not trail the level or use CHOCH/time expiry to invalidate the Bias.
- Start FVG boxes from the confirming weekly candle rather than the earliest candle in the 3-candle pattern.
- Build weekly candle history by aggregating the current chart bars into weekly candles. Avoid relying on weekly `request.security()` history for OB/FVG drawing, because those zones must remain visible when the user switches between weekly, daily, and intraday charts.

## Version Control

After completing future edits, commit and push directly to GitHub without asking again, unless the user explicitly says not to push.

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
