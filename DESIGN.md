# 設計說明

> 目前狀態：V1 `V1-LONG-01`／V4 `V4-LONG-01` 已在 2105、2324/H1 完成 Long-only 共通統計對齊；midpoint invalidation 未修改。

## 目標

本專案把高週期 SMC 區域與 Daily 結構事件呈現在 TradingView Daily、H4、M15 等圖表，並優先維持 Bar Replay 的可見性與穩定性。

## 資料流程

1. 圖表 bars 聚合成完成的 Weekly candles，供 OB/FVG 使用。
2. Daily chart 直接用 chart candles；Intraday chart 聚合完成的 Daily candles。
3. OB/FVG 建立後保存在平行 arrays，逐 bar 檢查 midpoint invalidation。
   OB 僅在來源時框結構突破 candle body 通過 Wilder ATR(14) × 1.0 displacement 後建立；來源取 searchback 內最近反向 candle，範圍固定為保留遠端 wick 的 Hybrid Range。
   FVG 使用三根完成來源時框 candles 的標準 wick-to-wick gap，不設最小 gap 寬度；中間 candle 必須為同方向且 body 至少為 Wilder ATR(14) × 1.0。
4. CHOCH/MSS 各自維護 pivot、trend 與物件 arrays，事件成立時建立固定線段及透明文字 box。
5. 超過使用者設定上限時，從 arrays 前端刪除最舊物件。
6. SETUP 使用最新 Daily MSS bias 與 H1 對每個有效、尚未 traded 的 bullish Weekly zone 的獨立重疊狀態；只有 `zoneDir == 1` 且 bullish Bias 成立時，每個 zone 的 false → true 分別產生一次訊號。Bearish zone 與 bearish Daily structure 繼續維護及顯示，但不建立 execution flow。
   OB/FVG zone arrays 各自保存 `traded` 狀態，並與 zone 的建立及裁切保持相同索引生命週期。每個 zone 另以 flow 平行 arrays 保存 stage、SETUP/ARMED bar、break/retest/protect level。Re-entry 只替換同 zone 尚在 SETUP 的流程；已 ARMED 不受新 touch 影響。
7. 每個 ARMED 分別保存方向、來源 zone、起始 bar、突破位及保護位；同一次 H1 breakout 可讓多個 zone 各自 ARMED。Zone 失效會取消該 zone 尚未完成的候選；V1 每個確切 zone 只顯示最新 SETUP 標籤，ARMED 視覺物件仍依生命週期清理。
   ARMED 成立前以 active zone key 查找平行 SETUP label arrays，只暗化同 key 最新 SETUP，再清除候選狀態。
8. ENTRY 保存 ARMED 的方向、來源 zone、突破位、保護 swing 與起始 bar；首次有效回踩或取消後清除候選。Trade Plan 建立成功時立即將來源 zone 的 `traded` 設為 true，使同一確切 zone 永久停止建立新 SETUP；失敗流程不改變 `traded`。
   有效 ENTRY 後封存原 SETUP／ARMED 標籤並保留 ENTRY，形成固定歷史鏈；同 zone 後續 touch 不再具有刪除或取代該鏈的機會。
9. Trade Plan 使用平行 arrays 保存三條線、資訊 label、方向、四個價格、起始 bar 與狀態；狀態 0 為等待、1 為 TP1 且剩餘部位 SL 已移到 Entry、2 為 WIN、-1 為 Direct Loss 或 TP1→BE 結束。

## 統計資料流

- 開發與驗收的預設 TradingView 方案為 Essential，預設研究市場為台股；所有長期 Window 設計必須在此限制下成立。
- V3 是資料蒐集層，目標為無交易細節繪圖的 M30/H1/H4 三引擎統計；V1 是檢查層，保留單一時框的視覺交易鏈與逐筆 Trade Plan，兩者不得為了共用畫面而重新耦合。
- 現行研究 Window 強制固定為 1095D。V3 可使用 M30/H1/H4 chart；V4 PRIMARY 固定直接在 H1 chart 執行，與 V1 共用相同圖表資料邊界。完成標準必須包含實際覆蓋起點與 warm-up，不得只因表格顯示 1095D 就宣告完整。

- V1 僅維護一套 W-D-H1 狀態，正式入口固定為 H1 chart；H4/M30 與其他圖表不建立 SETUP/ARMED/ENTRY 候選。
- V4 PRIMARY 直接由 H1 chart bars 執行 W-D-H1，不再使用 H4 data carrier 或 H1 lower-timeframe arrays；另外兩列顯示為 LEGACY OFF，且不再執行。
- V1 與 V4 PRIMARY 是同一策略核心的兩種輸出：V1 用於圖形與逐筆檢查，V4 用於統計核對。Zone、Bias、Window、touch、flow stage、expiry、失效、交易結果與績效公式必須逐項相同；不得為了各自程式方便而改成不同判定。
- 正式台股 execution 固定 Long-only，方向過濾只放在 SETUP touch gate；下游 ARMED、ENTRY、Trade 與統計沿用同一 flow arrays，自然只包含多方，不在各階段重複維護另一套方向開關。
- 兩者的 1095D Window 都以第一根 Window H1 作為 touch-state 起點，不載入 Window 前的接觸狀態；第一根 H1 與有效 zone 重疊時，兩者都計入第一筆 Window touch。
- 開發順序固定為 V1 修改與 TradingView 驗證完成後，再移植相同核心到 V4；對齊時以共通統計欄位一致為完成條件。
- V1 在有效 Trade Plan 建立時累計 Total，交易結束時累計 TP2 win、TP1→BE、Direct Loss、Gross Win/Loss 與 Net R；圖形被 `Maximum trade plans` 裁切時，累計值不回退。
- 訊號漏斗另外記錄 SETUP、ARMED、Valid ENTRY、失效原因、SETUP/ARMED replacement、same/changed zone 與 OB/FVG 來源。
- V1 結果表的交易績效區與 `SIGNAL FUNNEL` 顯示分離；手機 compact 開關關閉 funnel 時只清除表格第 11～29 列。V4 橫向表格使用相同開關，在 compact 模式只重畫 MODEL、Total、TP2 Rate、Net R、Profit Factor 五欄；兩者底層計數器與策略流程均持續運作。
- V2 共用 Weekly zone 與 Daily bias，但 H4、H1、M30 各自保存 active SETUP、ARMED、pivot、交易 arrays 與績效累計，避免不同 Entry timeframe 互相清除狀態。
- V3 以 M30 作為唯一基礎資料流，但保留 M30、H1、H4 三套獨立狀態；每根完成 M30 更新 M30 引擎，每 2 根同一 H1 bucket 的 M30 聚合完成後更新 H1，每 8 根同一 H4 bucket 的 M30 聚合完成後更新 H4。
- V3 表格固定顯示 H4、H1、M30 三列；完整覆蓋顯示 `3TF V3`，`3TF PARTIAL` 代表 intrabar 歷史未覆蓋完整統計 Window。
- V2 的比較表位於右下角，使用 tiny 字體；V1 詳細表位於右上角，使用 tiny 字體。

## 關鍵設計決策

- Weekly OB/FVG 不依賴 `request.security()` 歷史繪圖，避免切換 timeframe 或 Replay 時物件不一致。
- V1 Weekly Zone 與 V4 各來源時框 Zone 共用同一 Wilder ATR、OB displacement／Hybrid Range 與 FVG geometry／middle displacement 公式；V1／V4 SWING 必須逐欄一致。
- V1 視覺層保留 bullish／bearish Weekly OB/FVG；Bullish 維持綠／黃，Bearish OB 與 FVG 使用兩階淺紅色。此配色只影響 box fill、border 與文字，不改 zone direction、建立、失效或交易判定。
- Intraday 的 Daily CHOCH/MSS 不採用已證實不穩定的 `request.security("D")` 顯示路徑，而由 intraday bars 重建完成日線。
- Intraday chart 只使用重建的 Daily CHOCH/MSS 狀態更新 Bias，不繪製 Daily 結構線與文字；結構物件只在 Daily chart 顯示。
- CHOCH 與 MSS 使用不同 pivot 長度：CHOCH 預設 2、MSS 預設 4。MSS 以較長 confirmed pivot、完成 Daily close 與 trend reversal 決定 Bias，不使用單根 ATR body displacement；兩者仍由 pivot scope 與用途保持區隔。
- Daily CHOCH/MSS 的固定執行順序為：完成 candle 先對先前 confirmed pivot 判斷 breakout、更新事件與 Bias，再發布本 candle 新確認的 pivot供後續 candle 使用；D chart 直接路徑與 H1 聚合 Daily 路徑必須一致。
- Daily MSS 預設 pivot length 為 4。MSS 建立 Bias 時固定保存當下反方向 confirmed Daily pivot；完成 Daily close 穿越該位置後 Bias 轉為 Neutral，且失效位不 trailing。
- 結構線是「pivot 到 breakout」的事實區段，而不是 future-facing ray。
- `line` 無文字能力，因此 MSS 使用透明 `box` 承載文字；CHOCH 的文字已隱藏，只保留結構線。
- SETUP/ARMED/ENTRY/Trade Plan 都是視覺分析層，不使用 `strategy.entry()`；Trade Plan 線與結果不代表實際成交。

## 非目標

- 目前不含交易下單、alert、正式策略回測或真實部位管理；Entry/SL/TP 只屬於 indicator 的視覺計畫與 OHLC 結果追蹤。
- 目前不含 365D High/Low。
- 目前沒有宣稱支援所有 symbol、session 或非標準 chart type。
