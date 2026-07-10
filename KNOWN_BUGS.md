# Known Bugs and Limitations

## 已知限制

- TradingView/Pine 無本機自動測試環境；Repository 的靜態檢查不能取代 Pine Editor compile 與圖表驗證。
- Intraday Daily candle 是由目前 chart bars 聚合；session、缺失 bars、非標準 chart type 對結果的影響尚未建立測試證據。
- 結構繪圖仍為每條 CHOCH/MSS 建立 line 與透明 text box；CHOCH 雖隱藏文字，仍會消耗 box 配額。
- OB/FVG 的 invalidation 使用目前 chart bar 的 `close`，不同 chart timeframe 可能使停止延伸的精確時間不同；目前尚無跨 timeframe 驗收紀錄。
- `High timeframe` 是可調 input，但現有規格、命名與測試歷史均以 Weekly 為基準；其他值未宣告為已驗證。
- SETUP 尚未完成 Daily、H4、M15 與 Replay 視覺驗證；因 zone touch 使用目前圖表 K 棒 high/low，不同圖表時框的首次觸及時間可能不同。
- ARMED 使用目前圖表時框 pivot 與 ATR，因此同一 SETUP 在 Daily、H4、M15 可能產生不同結果；這是目前設計，但尚未完成驗收。
- ENTRY 的回踩與保護 swing 觸及同樣使用目前圖表 K 棒 OHLC；不同時框的 ENTRY 時間與是否取消可能不同，尚待 Replay 驗收。
- ENTRY expiry 預設關閉，長時間未回踩的 ARMED 候選會持續存在，直到 zone/bias/protect/new SETUP 任一取消條件成立。
- Trade Plan 只使用圖表 OHLC；同一 K 內 SL/TP 的真實先後不可知，目前固定採 SL 優先，與更低時框實際路徑可能不同。
- 若 ENTRY 當下沒有有效的反方向 confirmed pivot，或 SL 不在 Entry 的正確方向／距離不足一個最小跳動，ENTRY 標籤仍可出現，但不建立 Trade Plan。

## 已修正的歷史問題

- 同一 OB 來源 candle 重複建立：已加入來源時間與方向去重。
- 365D High/Low 造成額外 Replay 資源壓力：功能已移除。
- Daily CHOCH/MSS 只在 Daily chart 顯示：已改為在 Intraday chart 聚合完成 Daily candles；仍需完整視覺回歸測試。
- CHOCH/MSS 向未來延伸：已改為 pivot 到 breakout candle 的固定線段。
