# Roadmap

Roadmap 只列中長期方向；當前工作以 `TODO.md` 為準。

## 近期

- 以最終 `V1-LONG-01`／`V4-LONG-01` 在 2376/H1 建立 current-build 回歸基準。
- 若使用者重啟 zone invalidation 議題，先只在 V1 比較 midpoint 與 full-edge close invalidation，不與其他策略規則同時修改。
- 建立 OB/FVG 分組交易統計。

## 中期

- 用 V3 對多檔台股進行三年跨週期統計。
- 分析跨股票一致性、移除最佳單一股票後的結果與參數穩健度。
- 將訊號判定與繪圖管理進一步分離，降低回歸風險。
- 策略凍結後才進行 paper trade／極小部位驗證。

## 暫不納入

- 自動下單或券商連線。
- 績效保證。
- 未經規格確認的其他平台移植。
