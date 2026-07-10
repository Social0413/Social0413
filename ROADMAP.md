# Roadmap

Roadmap 只列尚未完成或需要確認的方向，不代表已承諾版本或日期。

## 近期

- 在 TradingView 對 Daily、H4、M15 執行同一期間的視覺比對，確認 Daily CHOCH/MSS 對齊。
- 補齊 Replay 測試矩陣：逐 bar 前進、切換 timeframe、midpoint invalidation、物件上限。
- 建立可重複的 release checklist，將 Pine Editor compile 與圖表截圖結果記錄於 `TEST_RESULT.md`。
- 釐清 `High timeframe` 是否保留為可調參數，或正式限定 Weekly。

## 中期候選

- 將訊號判定與繪圖物件管理進一步拆分，降低修改規格時的回歸風險。
- 評估 alert conditions；必須先定義事件只觸發一次的規則。
- 評估效能診斷開關，但不得恢復會造成大量 label 的舊 debug 作法。

## 暫不納入

- 自動交易與績效保證。
- 未經規格確認的 Python、MT5 或 MultiCharts 移植。
