# Known Bugs and Limitations

本檔只記錄目前仍影響開發的問題。已修正歷史請查 `PROJECT_HISTORY.md` 與 `TEST_RESULT.md`。

## P0：目前無已知阻斷問題

- V1 `V1-PZ-04 / FULL` 與 V4 `V4-PZ-04 / FULL` 已在 1504、2105、2324/H1 通過顯示、執行與共通統計對齊驗證。
- ARMED／ENTRY 現有流程可執行，但規則尚未精修；這是下一階段工作，不視為 SETUP 阻斷問題。
- 已修正的負索引錯誤、失敗嘗試與 rollback 請查 `PROJECT_HISTORY.md`。

## V4 限制

- PRIMARY 固定使用 H1；其他 timeframe 只顯示 `Use H1 chart`。
- LEGACY `D-H4-H1`、`H4-H1-M30` 目前關閉，不參與策略或驗收。
- V4 已在 `V4-PZ-04` 移植 V1 已驗證的兩段式負索引檢查並預設 `FULL`；TradingView compile／runtime 與三檔共通統計核對均已通過。
- V4 是統計研究引擎，不重畫 V1 的全部 SETUP／ARMED／ENTRY／Trade Plan 視覺物件。

## 一般限制

- 本機沒有 Pine compiler；Repository 靜態檢查不能取代 TradingView compile 與實圖驗證。
- TradingView Essential 的歷史資料、物件與執行限制可能影響長 Window 結果。
- OB/FVG midpoint invalidation 使用圖表 bar close；切換 timeframe 可能改變失效時點，因此正式入口固定為 H1。
- ENTRY expiry 預設關閉；候選可能持續到 zone、bias 或 protect 條件失效。
- 指標只做分析與模擬，不連接券商、不自動下單，也不保證績效。
