# Known Bugs and Limitations

本檔只記錄目前仍影響開發的問題。已修正歷史請查 `PROJECT_HISTORY.md` 與 `TEST_RESULT.md`。

## P0：目前無已知阻斷問題

- 目前穩定版為 V1 `V1-LONG-01`、V4 `V4-LONG-01`。
- 2105、2324/H1／1095D／FULL 的 Long-only 共通統計已完成 TradingView 對齊。
- V1/V4 的 ENTRY expiry、TP1→BE、手機 COMPACT 表格與 Long-only gate 均已完成實圖或共通數值驗證。

## 尚待補充的回歸

- 2376 曾在較早的 ENTRYTPSL／MSS 階段完成多輪驗證，但尚未用最終 `LONG-01` current build 做一次 V1/V4 FULL 共通統計回歸。這不是目前阻斷問題，應作為下一輪 zone invalidation 前的基準。

## V10 限制

- `V10-BASELINE-01`只把已提供15檔ETH H1／1825D FULL截圖的ENTRY-05行為凍結並更換BUILD識別；6669／Daily已確認新BUILD與runtime顯示，但ETH H1 execution回歸仍待第二批開始前確認。首批數據仍未扣手續費、交易稅與滑價，不代表可實盤期望值。
- ENTRY-05首批15檔已提供FULL、FROM／TO及跨標的來源統計，但Window第一根H1、資料不足PART、Weekly Bias撤銷pending、same-bar、reload／Replay仍未逐項驗證；Baseline命名不解除這些限制。
- `V10-DH1-ENTRY-04`修正2360／ETH H1發現的共同lifecycle問題：ENTRY-03在H1 close離開zone後凍結SETUP tracking，後續lower low不移動marker，且close跌破舊SETUP low會在Weekly仍Bullish時取消candidate並停止break line。ENTRY-04的2360指定路徑已確認marker移動、break line延伸與後續OB ARM；Weekly Bias／Daily zone兩種hard invalidation、same-bar、reload／Replay及2317／2105完整回歸仍待驗證。
- `V10-DH1-ENTRY-01`已在2317／ETH H1 compile／runtime，但把ARM下一根open直接視為ENTRY，屬需求誤解，已由midpoint Buy Limit的`V10-DH1-ENTRY-02`取代，不得恢復。
- `V10-DH1-ENTRY-03`已在2317／2105 ETH H1通過compile／runtime與來源統計首輪實圖：OB+FVG各階段及NET R均與全域總計閉合，TP1 HIT亦涵蓋2105後續BE。尚未以實圖證明Weekly Bias改變撤銷pending Buy Limit、ENTRY same-bar SL+TP只進SL與紫紅marker，以及reload／Replay不重複累加；這些是補充回歸，非目前阻斷問題。
- `V10-DH1-ARMED-01` 已在 2324／ETH H1 compile／runtime，但把 zone exit 等同取消 ARM candidate；畫面有 Bullish Daily OB SETUP 與後續結構上漲，右上仍為 ARMED `0 / 0 ACTIVE`。此 lifecycle 已被淘汰，不得恢復。
- `V10-DH1-ARMED-02` 已在 2324／2634／2317 ETH H1 通過 compile／runtime 與第一輪 marker／count 視覺檢查；`V10-DH1-ARMED-03` 診斷線亦在 2317／ETH H1 通過視覺檢查，ARM 階段可收尾。2634 可見 SETUP 未 ARM的 exact原因、三項取消原因逐筆證據、reload與 Replay仍未完成，後續 ENTRY 開發必須列為 ARM regression，不得宣稱已逐項證明。
- `V10-DH1-SETUP-02R1` 已在 2324／ETH H1 通過 compile／runtime 與同-event BOS 去重；但 First-touch re-entry block、lower-low marker move、reload 與 Replay 尚未逐項留下獨立證據，ARMED 開發時必須列為 SETUP regression。
- R1 已修正 SETUP-02 缺少 BOS display identity 的問題；後續不得移除 `direction + BOS time` key，也不得讓 line／label／key 使用不同 trimming lifecycle。
- `V10-DH1-SETUP-01` 已在 2105／ETH H1 顯示 marker 與 `184 / 0 ACTIVE`，但允許同 exact zone re-entry 重建 SETUP，導致 TOTAL 膨脹；此規則已被明確淘汰，不得恢復。
- `V10-FVG-03` 的 0.50 ATR gate 已在 2105／Daily 確認上下兩個指定微小 FVG消失且其他主要 FVG仍可見；但 2324／2634、ETH H4/H1、K1/K2/K3 exact metadata、跨時框失效 endpoint、reload 與 Replay 尚未完成，不能把單一 Daily 截圖視為完整 canonical reconciliation。
- TradingView 同一台股 symbol 的 Daily EOD 與 H1 RTH 可能不含相同成交。2105／2023-12-21 已確認 Daily 與 H1 ETH 到達 47.90，但 H1 RTH 看不到該價；這不是除權息，也不是 OB/BOS engine 錯誤。`V10-DZONE-09` 已把 canonical requests 固定為 ETH 並加入 SESSION 警告，但 Pine 無法替使用者切換原生圖表，RTH 截圖不得作為跨時框驗收依據。
- `V10-DZONE-06` 的斜向 source trace 不得沿用。`V10-DZONE-07` 的 broken-pivot-to-BOS 水平線已通過 2324／2634 Daily 視覺確認；H4/H1 exact endpoints 仍待核對。
- `V10-DZONE-08` 已把 Daily OB source 改成 pivot-to-BOS 開區間的極值反向 K，目前只有 Repository 靜態檢查；必須驗證最低 bearish／最高 bullish 選擇、同價 tie-break、無候選事件及跨時框一致性。
- `V10-DZONE-03` 已在 2324 實圖確認跨時框失敗：Daily 顯示 Bullish OB，H1 在相同區段只剩 FVG；同時 Weekly flips 為 Daily `20/20`、H1 `10/10`，證明 chart-driven 高週期 state 會受載入歷史起點影響。此版不得作為後續 execution 基準。
- `V10-DZONE-04` 已移除 chart-driven Daily OHLC／ATR／pivot 聚合，改用 canonical confirmed-Daily request；2324 Daily/H4/H1 compile/runtime 與第一輪 zone 位置視覺通過。
- `V10-DZONE-05` 已將 Weekly pivot、Bias、flip counts 與 markers 改為 canonical confirmed-Weekly request；2324 Weekly／Daily／H4／H1 table 已共同顯示 Bullish、`47.75`、`27.50`、`8 / 7`，跨時框表格對齊通過。
- Daily zones 只支援 Daily 與 intraday charts；Weekly 等較高時框只顯示 Weekly Bias，右上表標記 `USE D / INTRADAY`。
- Canonical Daily zones 尚未證明「所有已載入歷史 zones」精確數值完全一致；必須以游標／Data Window 逐一核對 OB/FVG source time、top、bottom 與失效終點，並測試 reload／Replay。
- `V10-DZONE-05` 尚未逐一證明歷史 W BULL／W BEAR marker 每個 canonical flip 永遠只發布一次，也尚未完成切換時框、reload 與 Replay 穩定性測試；在這些 audit 完成前仍不加入 H1 execution。

## V4 限制

- PRIMARY 固定使用 H1；其他 timeframe 只顯示 `Use H1 chart`。
- LEGACY `D-H4-H1`、`H4-H1-M30` 目前關閉，不參與策略或驗收。
- V4 是統計研究引擎，不重畫 V1 的 Weekly zones、Daily structure、SETUP／ARMED／ENTRY 或 Trade Plan 視覺物件。

## 一般限制

- 本機沒有 Pine compiler；Repository 靜態檢查不能取代 TradingView compile 與實圖驗證。
- TradingView Essential 的歷史資料、物件與執行限制可能影響長 Window 結果。
- 穩定版 V1／V4 Weekly OB/FVG 仍使用 H1 close 穿越 midpoint 失效；V10 Daily FVG 亦維持 midpoint。V10 Daily OB 已改為 completed Daily close 穿越 full edge 才失效。
- ENTRY expiry 預設 15 根 H1；輸入設為 0 時仍可關閉。
- 指標只做分析與模擬，不連接券商、不自動下單，也不保證績效。
