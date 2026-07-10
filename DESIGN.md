# 設計說明

## 目標

本專案把高週期 SMC 區域與 Daily 結構事件呈現在 TradingView Daily、H4、M15 等圖表，並優先維持 Bar Replay 的可見性與穩定性。

## 資料流程

1. 圖表 bars 聚合成完成的 Weekly candles，供 OB/FVG 使用。
2. Daily chart 直接用 chart candles；Intraday chart 聚合完成的 Daily candles。
3. OB/FVG 建立後保存在平行 arrays，逐 bar 檢查 midpoint invalidation。
4. CHOCH/MSS 各自維護 pivot、trend 與物件 arrays，事件成立時建立固定線段及透明文字 box。
5. 超過使用者設定上限時，從 arrays 前端刪除最舊物件。

## 關鍵設計決策

- Weekly OB/FVG 不依賴 `request.security()` 歷史繪圖，避免切換 timeframe 或 Replay 時物件不一致。
- Intraday 的 Daily CHOCH/MSS 不採用已證實不穩定的 `request.security("D")` 顯示路徑，而由 intraday bars 重建完成日線。
- CHOCH 與 MSS 使用不同 pivot 長度；MSS 另加 ATR body displacement，避免兩者退化成同一訊號。
- 結構線是「pivot 到 breakout」的事實區段，而不是 future-facing ray。
- `line` 無文字能力，因此使用透明 `box` 承載文字；這會同時消耗 box 資源。

## 非目標

- 目前不含交易下單、alert、回測績效或策略部位管理。
- 目前不含 365D High/Low。
- 目前沒有宣稱支援所有 symbol、session 或非標準 chart type。
