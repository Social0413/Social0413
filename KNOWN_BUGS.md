# Known Bugs and Limitations

## 已知限制

- V3 的 `FULL` 目前只檢查各引擎第一筆可用資料是否早於統計 Window 起點，尚未顯示實際起訖日期、warm-up 狀態或偵測 Window 中間的缺失 bars；`FULL` 不等同資料供應商逐根無缺漏保證。
- Essential 的 M30 chart 約 10,000 根 intraday bars，無法保證直接顯示台股 5 年／7 年全部 M30 細節；V3 可在 H4 chart 以 lower-timeframe arrays 統計，但 V1 不一定能視覺重現早期 M30 交易。因此近期規則開發聚焦可完整檢查的最近三年。
- V3 純統計模式仍保留部分不會被呼叫的舊繪圖函式與單引擎狀態程式；目前以固定 false 開關避免建立物件，尚未完成完整 dead-code 移除與執行效能量測。

- V2 Compare 目前僅支援 M30 圖表。Pine 切換圖表週期會重新執行腳本；尚未完成以 M30 intrabar arrays 在 H1/H4 圖表重建完全相同統計。
- 曾嘗試以 `request.security(..., "30", compareSnapshot())` 固定計算，但 Pine Editor 回報 `line.set_x2 cannot be used with any parameter of security()`；該路徑已移除，不能視為跨週期支援。
- V2 保留 V1 的繪圖與單週期程式，再追加 Compare 引擎；雖然 V2 預設關閉單週期標籤、Trade Plan 與詳細表格，仍有額外計算與物件配額壓力，尚未完成長時間 Replay 壓力測試。
- V2 三週期 Compare 已在 M30 圖表顯示，但尚未逐筆與三次 V1（H4/H1/M30）建立完整一致性驗收紀錄。
- V3 已完成 Pine Editor compile，且使用者在同一 symbol、365D Window 的 M30/H1/H4 圖表取得一致三列結果。此驗收只涵蓋已提供的市場與期間，不代表所有 symbol、session 或 Window 均已驗證。
- H1/H4 的 lower-timeframe 歷史受 TradingView plan、symbol 與可用歷史深度限制；`3TF PARTIAL` 表示統計 Window 不完整。V3 仍共用主程式建立的 Weekly zone 與 Daily bias 狀態，其他市場若出現跨圖差異，需再將 zone/bias 移入純 M30 數值核心。

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
