# Known Bugs and Limitations

本檔只記錄目前仍影響開發的問題。已修正歷史請查 `PROJECT_HISTORY.md` 與 `TEST_RESULT.md`。

## P0：目前無已知阻斷問題

- 目前穩定版為 V1 `V1-LONG-01`、V4 `V4-LONG-01`。
- 2105、2324/H1／1095D／FULL 的 Long-only 共通統計已完成 TradingView 對齊。
- V1/V4 的 ENTRY expiry、TP1→BE、手機 COMPACT 表格與 Long-only gate 均已完成實圖或共通數值驗證。

## 尚待補充的回歸

- 2376 曾在較早的 ENTRYTPSL／MSS 階段完成多輪驗證，但尚未用最終 `LONG-01` current build 做一次 V1/V4 FULL 共通統計回歸。這不是目前阻斷問題，應作為下一輪 zone invalidation 前的基準。

## V4 限制

- PRIMARY 固定使用 H1；其他 timeframe 只顯示 `Use H1 chart`。
- LEGACY `D-H4-H1`、`H4-H1-M30` 目前關閉，不參與策略或驗收。
- V4 是統計研究引擎，不重畫 V1 的 Weekly zones、Daily structure、SETUP／ARMED／ENTRY 或 Trade Plan 視覺物件。

## 一般限制

- 本機沒有 Pine compiler；Repository 靜態檢查不能取代 TradingView compile 與實圖驗證。
- TradingView Essential 的歷史資料、物件與執行限制可能影響長 Window 結果。
- OB/FVG 目前使用圖表 bar close 穿越 midpoint 失效；正式入口固定 H1。Full-edge close invalidation 尚未實作。
- ENTRY expiry 預設 15 根 H1；輸入設為 0 時仍可關閉。
- 指標只做分析與模擬，不連接券商、不自動下單，也不保證績效。
