# 設計說明

## 目標

本專案把高週期 SMC 區域與 Daily 結構事件呈現在 TradingView Daily、H4、M15 等圖表，並優先維持 Bar Replay 的可見性與穩定性。

## 資料流程

1. 圖表 bars 聚合成完成的 Weekly candles，供 OB/FVG 使用。
2. Daily chart 直接用 chart candles；Intraday chart 聚合完成的 Daily candles。
3. OB/FVG 建立後保存在平行 arrays，逐 bar 檢查 midpoint invalidation。
4. CHOCH/MSS 各自維護 pivot、trend 與物件 arrays，事件成立時建立固定線段及透明文字 box。
5. 超過使用者設定上限時，從 arrays 前端刪除最舊物件。
6. SETUP 使用最新 Daily MSS bias 與目前 K 棒對有效 Weekly zone 的重疊狀態；重疊狀態由 false 轉為 true 時顯示一次。
   SETUP label 與 zone key 使用平行 arrays；同 key 新訊號先刪除仍在等待的舊 label 再移到 arrays 尾端。ARMED 成立時將 key 改為 archived key，使完成流程的 SETUP 不會被後續同 zone 訊號刪除。
7. ARMED 保存 SETUP 方向、來源 zone、起始 bar 與待突破 pivot；zone 失效、bias 反轉、逾期或突破成立時清除狀態。
   ARMED 成立前以 active zone key 查找平行 SETUP label arrays，只暗化同 key 最新 SETUP，再清除候選狀態。
8. ENTRY 保存 ARMED 的方向、來源 zone、突破位、保護 swing 與起始 bar；首次有效回踩、取消或新 SETUP 後清除候選。
9. Trade Plan 使用平行 arrays 保存三條線、資訊 label、方向、四個價格、起始 bar 與狀態；狀態 0 為等待、1 為 TP1、2 為 WIN、-1 為 LOSS。

## 統計資料流

- 開發與驗收的預設 TradingView 方案為 Essential，預設研究市場為台股；所有長期 Window 設計必須在此限制下成立。
- V3 是資料蒐集層，目標為無交易細節繪圖的 M30/H1/H4 三引擎統計；V1 是檢查層，保留單一時框的視覺交易鏈與逐筆 Trade Plan，兩者不得為了共用畫面而重新耦合。
- 長期研究 Window 固定為 1095D、1825D、2555D，不開發 3650D。Essential 下，台股 1095D 約 6,750 根 M30，通常可在 M30 chart 執行；1825D 與 2555D 約需 11,250 與 15,750 根 M30，應以 H4 chart 承載主資料集，再請求 M30/H1 intrabars。完成標準必須包含實際覆蓋起點與 warm-up，不得只因輸入顯示指定天數就宣告完整。

- V1 僅維護一套狀態，Entry timeframe 直接跟隨目前 H4/H1/M30 chart；切換 chart timeframe 時 Pine 重新執行並計算該週期結果，不保留手動 Entry timeframe input。
- V1 在有效 Trade Plan 建立時累計 Total，交易結束時累計 TP2 win、TP1→Loss、Direct Loss、Gross Win/Loss 與 Net R；圖形被 `Maximum trade plans` 裁切時，累計值不回退。
- 訊號漏斗另外記錄 SETUP、ARMED、Valid ENTRY、失效原因、SETUP/ARMED replacement、same/changed zone 與 OB/FVG 來源。
- V2 共用 Weekly zone 與 Daily bias，但 H4、H1、M30 各自保存 active SETUP、ARMED、pivot、交易 arrays 與績效累計，避免不同 Entry timeframe 互相清除狀態。
- V3 以 M30 作為唯一基礎資料流，但保留 M30、H1、H4 三套獨立狀態；每根完成 M30 更新 M30 引擎，每 2 根同一 H1 bucket 的 M30 聚合完成後更新 H1，每 8 根同一 H4 bucket 的 M30 聚合完成後更新 H4。
- V3 表格固定顯示 H4、H1、M30 三列；完整覆蓋顯示 `3TF V3`，`3TF PARTIAL` 代表 intrabar 歷史未覆蓋完整統計 Window。
- V2 的比較表位於右下角，使用 tiny 字體；V1 詳細表位於右上角，使用 tiny 字體。

## 關鍵設計決策

- Weekly OB/FVG 不依賴 `request.security()` 歷史繪圖，避免切換 timeframe 或 Replay 時物件不一致。
- Intraday 的 Daily CHOCH/MSS 不採用已證實不穩定的 `request.security("D")` 顯示路徑，而由 intraday bars 重建完成日線。
- CHOCH 與 MSS 使用不同 pivot 長度；MSS 另加 ATR body displacement，避免兩者退化成同一訊號。
- 結構線是「pivot 到 breakout」的事實區段，而不是 future-facing ray。
- `line` 無文字能力，因此 MSS 使用透明 `box` 承載文字；CHOCH 的文字已隱藏，只保留結構線。
- SETUP/ARMED/ENTRY/Trade Plan 都是視覺分析層，不使用 `strategy.entry()`；Trade Plan 線與結果不代表實際成交。

## 非目標

- 目前不含交易下單、alert、正式策略回測或真實部位管理；Entry/SL/TP 只屬於 indicator 的視覺計畫與 OHLC 結果追蹤。
- 目前不含 365D High/Low。
- 目前沒有宣稱支援所有 symbol、session 或非標準 chart type。
