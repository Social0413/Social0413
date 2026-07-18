---
name: smc-weekly-ob-fvg
description: Maintain the TradingView SMC Replay Toolkit for Taiwan equities: Weekly OB/FVG, Daily MSS bias, H1 Long-only SETUP/ARMED/ENTRY, Trade Plans, and V1/V4 statistical alignment.
---

# SMC Weekly OB FVG

## Authority and assets

- Current behavior is defined by `../SMC_SPEC.md`; implementation architecture is in `../DESIGN.md`.
- V1 visual inspection: `assets/smc_weekly_ob_fvg_v1.pine`.
- V4 PRIMARY statistical reconciliation: `assets/smc_top_down_models_v4.pine`.
- V10 frozen research baseline: `assets/smc_weekly_structure_bias_v10.pine`; current `V10-BASELINE-01` is behavior-identical to ENTRY-05 and retains ENTRY-04 continuous SETUP tracking, ENTRY-03 source counters and ENTRY-02 midpoint Buy Limit. It fixes execution and statistics to 1825 calendar days, warms Weekly/Daily zones and confirmed H1 pivots before the Window, creates no execution state before the Window, and reports `1825D FULL/PART` plus actual FROM/TO. Only FULL results are directly comparable across symbols. Do not tune Baseline from reviewed samples; validate hypotheses on a new fixed batch. Same-bar SL+TP counts only as SL; V1／V4 remain unchanged.
- `V10-DZONE-03` failed 2324 Daily/H1 visual reconciliation because chart-driven Daily state produced OB only on Daily. `V10-DZONE-04` calculates Daily ATR, pivots, BOS, OB source and FVG events inside one confirmed Daily request context; 2324 Daily/H4/H1 first visual zone reconciliation passed, while exact values, invalidation dates, reload and Replay remain pending.
- `V10-DZONE-05` moves Weekly pivots, Bias, flip counts and markers into one confirmed Weekly request context. Its Weekly table passed 2324 Weekly/Daily/H4/H1 reconciliation at Bullish, `47.75`, `27.50`, and `8 / 7`; marker one-shot, reload/Replay, and exact-zone audit remain pending before execution work begins.
- `V10-DZONE-06` source-candle trace was a requirement misunderstanding and is superseded. `V10-DZONE-07` draws each OB-producing BOS horizontally from the broken confirmed pivot candle to the BOS candle at the broken swing price; this must match across Daily/H4/H1 before being treated as visually verified.
- `V10-DZONE-08` replaces the fixed 8-bar nearest-opposing source rule: Bullish selects the lowest-low bearish candle and Bearish the highest-high bullish candle strictly between pivot and BOS; equal extremes choose the later candle, endpoints/Doji are excluded, and no opposing candle means no OB.
- `V10-DZONE-09` pins both canonical requests to `session.extended`; `V10-FVG-01` adds the isolated FVG timing fixes, FVG-02 tests 0.10 ATR and FVG-03 tests 0.50 ATR without the historical K3 half-range condition. Daily/H4/H1 Replay and future H1 execution validation must use ETH; Pine cannot switch native chart bars, so non-ETH intraday charts must show `USE ETH (...)` and are invalid for cross-timeframe acceptance.
- Current stable builds are V1 `V1-LONG-01` and V4 `V4-LONG-01`.
- Core changes must be implemented and verified in V1 before the same logic is synchronized to V4.

## Current model

- Formal model: `Weekly Zone → Daily MSS Bias → H1 SETUP / ARMED / ENTRY`.
- Stable V1／V4 chart and statistics boundary: H1, 1095D, `FULL`. Current V10 boundary: ETH H1, 1825D, `FULL`; `PART` is diagnostic only and cannot enter direct cross-symbol ranking.
- Taiwan equity execution and all performance statistics are fixed Long-only.
- Bearish Weekly zones and bearish Daily structure remain visible as risk context, but cannot create execution flows.

## Weekly OB/FVG

- Build completed Weekly candles from chart bars; do not depend on Weekly `request.security()` history for zone drawing.
- Bullish OB: completed Weekly close breaks recent structure high and breakout body is at least Weekly Wilder ATR(14) × 1.0; use the nearest prior bearish candle within searchback.
- Bearish OB: symmetric close below structure low and nearest prior bullish candle.
- OB range is Hybrid Range: bullish `low → open`, bearish `open → high`.
- FVG uses the standard three-completed-candle wick-to-wick gap with no minimum gap width.
- The middle FVG candle must match direction and have body at least its own source-timeframe Wilder ATR(14) × 1.0; V10 records K1 first, K2 displacement/source and K3 confirmation/event time separately.
- Bullish OB is green and Bullish FVG is yellow. Bearish OB/FVG remain visible in two light-red shades.
- V10 Daily OB invalidation is full-edge close based: bullish completed Daily close below bottom, bearish completed Daily close above top. V10 Daily FVG and the stable V1/V4 Weekly zones remain midpoint-based.
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
