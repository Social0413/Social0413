# Known Bugs and Limitations

本檔只記錄目前仍影響開發的問題。已修正歷史請查 `PROJECT_HISTORY.md` 與 `TEST_RESULT.md`。

## P0：H1 per-zone SETUP engine 會停止顯示

- 穩定基準：V1 `V1-PZ-01 / PZ OFF`、V4 `V4-PZ-02 / PZ OFF`。
- 啟用 per-zone `FULL` 後，指標可能在 H1 完全不顯示；H4/Daily 的 unsupported 提示仍正常。
- 已排除：H1 timeframe 判斷、Weekly Zone 基礎繪圖、Daily MSS、統計表，以及 V1/V4 同時載入。
- 尚未確認：問題位於 V1 `TOUCH` 的 Zone/SETUP 建立，或 `FULL` 才增加的 ARMED/ENTRY。
- `V1-PZ-02 / V4-PZ-03` 曾嘗試調整查找與 touch-state 結構，但沒有改善，已撤回；不得重複把此推測當成已確認根因。
- 下一個安全測試：只測 V1 `TOUCH`，不修改 V4。

## V4 限制

- PRIMARY 固定使用 H1；其他 timeframe 只顯示 `Use H1 chart`。
- LEGACY `D-H4-H1`、`H4-H1-M30` 目前關閉，不參與策略或驗收。
- per-zone 改寫前，V4 PRIMARY 曾與 V1 在 1504、2105、2324 的可比欄位一致；此結果不能證明目前 per-zone `FULL` 正常。
- V4 是統計研究引擎，不重畫 V1 的全部 SETUP／ARMED／ENTRY／Trade Plan 視覺物件。

## 一般限制

- 本機沒有 Pine compiler；Repository 靜態檢查不能取代 TradingView compile 與實圖驗證。
- TradingView Essential 的歷史資料、物件與執行限制可能影響長 Window 結果。
- OB/FVG midpoint invalidation 使用圖表 bar close；切換 timeframe 可能改變失效時點，因此正式入口固定為 H1。
- ENTRY expiry 預設關閉；候選可能持續到 zone、bias 或 protect 條件失效。
- 指標只做分析與模擬，不連接券商、不自動下單，也不保證績效。
