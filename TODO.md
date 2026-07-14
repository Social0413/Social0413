# TODO

本檔只保留目前可執行工作。已完成證據放在 `TEST_RESULT.md`，歷史決策放在 `PROJECT_HISTORY.md`。

## 現在：第三個問題 SETUP

- [ ] 1. 只使用 V1 `V1-PZ-01`，在 1504/H1 將 diagnostic mode 從 `OFF` 改成 `TOUCH`。
- [ ] 2. 記錄 `TOUCH` 是否顯示、SETUP 數量及 TradingView 是否有 runtime 訊息。
- [ ] 3. 若 `TOUCH` 正常，再測 V1 `FULL`；若消失，只檢查 ARMED/ENTRY 區段。
- [ ] 4. 找到確切失敗語句後，只修 V1，並用 1504、2105、2324/H1 回歸。
- [ ] 5. V1 通過後，才把相同核心移植到 V4；逐欄比對 SETUP、ARMED、Total、OB/FVG、replacement 與績效。
- [ ] 6. 驗證完成後移除或隱藏 diagnostic mode，更新版號與文件。

## SETUP 目標行為

- [ ] 每個 Weekly Zone 各有一條獨立 SETUP → ARMED → ENTRY 流程。
- [ ] 同一 Zone 同時只能有一個未完成流程；明確離開再進入才可替換仍在 SETUP 的流程。
- [ ] 新 SETUP 不取消已 ARMED 候選。
- [ ] Zone 失效刪除尚未 ENTRY 的 SETUP／ARMED／候選；已完成交易與統計保留。
- [ ] SETUP expiry 預設 15 根 H1。

## SETUP 完成後

- [ ] 檢查 ARMED 的 H1 pivot、ATR displacement、保護 swing 與失效原因。
- [ ] 檢查 ENTRY 的首次回踩、等待期限及失效條件。
- [ ] 新增 OB/FVG 分組交易統計：Trades、Win/Loss、TP1/TP2、Net R、Avg R、Profit Factor。
- [ ] 完整股票池回測後才考慮調整策略參數。

## 收尾條件

- [ ] TradingView compile。
- [ ] 1504、2105、2324/H1 實圖驗證。
- [ ] V1/V4 版號與結果一致。
- [ ] 依 `CLOSEOUT_CHECKLIST.md` 更新 MD、錯誤與學習。
