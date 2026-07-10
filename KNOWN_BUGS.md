# Known Bugs and Limitations

## 已知限制

- TradingView/Pine 無本機自動測試環境；Repository 的靜態檢查不能取代 Pine Editor compile 與圖表驗證。
- Intraday Daily candle 是由目前 chart bars 聚合；session、缺失 bars、非標準 chart type 對結果的影響尚未建立測試證據。
- 結構文字使用透明 box，因此 CHOCH/MSS 同時消耗 line 與 box 配額。
- OB/FVG 的 invalidation 使用目前 chart bar 的 `close`，不同 chart timeframe 可能使停止延伸的精確時間不同；目前尚無跨 timeframe 驗收紀錄。
- `High timeframe` 是可調 input，但現有規格、命名與測試歷史均以 Weekly 為基準；其他值未宣告為已驗證。

## 已修正的歷史問題

- 同一 OB 來源 candle 重複建立：已加入來源時間與方向去重。
- 365D High/Low 造成額外 Replay 資源壓力：功能已移除。
- Daily CHOCH/MSS 只在 Daily chart 顯示：已改為在 Intraday chart 聚合完成 Daily candles；仍需完整視覺回歸測試。
- CHOCH/MSS 向未來延伸：已改為 pivot 到 breakout candle 的固定線段。
