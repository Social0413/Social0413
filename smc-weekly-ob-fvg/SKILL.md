---
name: smc-weekly-ob-fvg
description: Maintain the TradingView SMC Replay Toolkit for Taiwan equities: Weekly OB/FVG, Daily MSS bias, H1 Long-only SETUP/ARMED/ENTRY, Trade Plans, and V1/V4 statistical alignment.
---

# SMC Weekly OB FVG

## Authority and assets

- Current behavior is defined by `../SMC_SPEC.md`; implementation architecture is in `../DESIGN.md`.
- V1 visual inspection: `assets/smc_weekly_ob_fvg_v1.pine`.
- V4 PRIMARY statistical reconciliation: `assets/smc_top_down_models_v4.pine`.
- Current stable builds are V1 `V1-LONG-01` and V4 `V4-LONG-01`.
- Core changes must be implemented and verified in V1 before the same logic is synchronized to V4.

## Current model

- Formal model: `Weekly Zone → Daily MSS Bias → H1 SETUP / ARMED / ENTRY`.
- Formal chart and statistics boundary: H1, 1095D, `FULL`.
- Taiwan equity execution and all performance statistics are fixed Long-only.
- Bearish Weekly zones and bearish Daily structure remain visible as risk context, but cannot create execution flows.

## Weekly OB/FVG

- Build completed Weekly candles from chart bars; do not depend on Weekly `request.security()` history for zone drawing.
- Bullish OB: completed Weekly close breaks recent structure high and breakout body is at least Weekly Wilder ATR(14) × 1.0; use the nearest prior bearish candle within searchback.
- Bearish OB: symmetric close below structure low and nearest prior bullish candle.
- OB range is Hybrid Range: bullish `low → open`, bearish `open → high`.
- FVG uses the standard three-completed-candle wick-to-wick gap with no minimum gap width.
- The middle FVG candle must match direction and have body at least Weekly Wilder ATR(14) × 1.0.
- Bullish OB is green and Bullish FVG is yellow. Bearish OB/FVG remain visible in two light-red shades.
- Current invalidation remains midpoint-based: bullish close below midpoint, bearish close above midpoint. Full-edge invalidation is not implemented.
- Keep at most 40 zones per type and prevent duplicate OB source zones.

## Daily structure

- Daily CHOCH and MSS use separate confirmed pivots; defaults are CHOCH 2 and MSS 4.
- Daily MSS is a completed-close trend reversal through the longer confirmed pivot; it has no ATR body displacement filter.
- Evaluate each completed Daily candle against previously confirmed pivots before publishing pivots confirmed by that candle.
- Intraday charts aggregate completed Daily candles for Bias state; Daily structure objects are drawn only on the Daily chart.
- Bearish MSS can cancel or block long candidates even though short execution is disabled.

## H1 Long-only execution

- Only bullish Bias plus an active, untraded bullish Weekly zone can create SETUP.
- Each exact zone owns an independent flow and can create at most one valid Trade Plan.
- SETUP freezes the last confirmed H1 swing high as break level; ARMED forms on a later H1 close crossover with no ATR displacement filter.
- SETUP/ARMED waiting expiry is 15 H1 bars.
- ENTRY forms after ARMED when price retests the frozen break level and closes back above it; ENTRY retest expiry defaults to 15 H1 bars.
- SL is the opposite confirmed H1 protect swing saved at ARMED.
- Default targets are TP1 1R, TP2 2R, 50% exit at TP1; after TP1 the remaining SL moves to Entry.
- Same-bar result priority is `SL → TP2 → TP1`.
- A successful Trade Plan immediately marks the exact source zone traded/consumed.

## Display

- V1 draws zones, Daily structure, SETUP/ARMED/ENTRY and Trade Plans.
- V4 PRIMARY is the numerical reconciliation layer; LEGACY rows remain OFF.
- `Show SETUP/ARMED/ENTRY statistics` defaults on. When off, V1 hides SIGNAL FUNNEL and V4 shows only MODEL, Total, TP2 Rate, Net R and Profit Factor.
- Display switches must never change signal state or accumulated statistics.

## Validation and version control

1. Update Spec/Design before changing behavior.
2. Modify V1 and run Repository static checks.
3. Have the user compile and visually validate V1 in TradingView.
4. Synchronize the same core to V4 and reconcile common fields on fixed symbols.
5. Record actual evidence in `../TEST_RESULT.md`; never mark untested behavior as passed.
6. Run `git diff --check` and inspect the complete diff.
7. Commit and push only when the user explicitly requests it.

## Boundaries

- Do not place trades, connect brokers or alter TradingView account settings.
- Do not change OB/FVG definitions, invalidation, colors or formal timeframe without an explicit task.
- Do not combine zone invalidation experiments with unrelated strategy adjustments.
