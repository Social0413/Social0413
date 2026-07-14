# TODO

本檔只保留目前可執行工作。已完成證據放在 `TEST_RESULT.md`，歷史決策放在 `PROJECT_HISTORY.md`。

## 現在：ARMED 精修

- [ ] 1. 先列出 V1/V4 現行 ARMED 條件：H1 confirmed pivot、ATR displacement、break level、protect swing、expiry 與失效條件。
- [ ] 2. 依「簡單乾淨、用統計確認穩定」原則，只選 2～3 個影響最大的調整建議，不展開大量參數矩陣。
- [ ] 3. 選定後只先修改 V1；由使用者在 TradingView 以 1504、2105、2324/H1／1095D／FULL 驗證。
- [ ] 4. V1 通過後才把同一 ARMED 核心原樣移植到 V4，核對 SETUP、ARMED、Total、replacement 與績效。

## 本階段不處理

- ENTRY、SL／TP 與 OB/FVG 分組績效延後，避免同時改動多個階段。
- V4 LEGACY 模型維持關閉。
- `OFF / TOUCH / FULL` 暫保留作為故障隔離開關；正式移除或隱藏另開小步驟處理。
