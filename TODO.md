# TODO

本檔只保留下一個可執行工作。已完成證據放在 `TEST_RESULT.md`，歷史決策放在 `PROJECT_HISTORY.md`。

## 下一步：驗證 V10-DZONE-09 ETH canonical feed + extreme opposing OB source

- [ ] 在 TradingView compile `V10-DZONE-09`，確認 2105、2324、2634／Daily 及 H4/H1 無 runtime／memory error，右上 BUILD 正確。
- [ ] H1 切為 RTH 時，確認右上 SESSION 顯示 `USE ETH (...)`；切為 ETH 後顯示 `ETH`。Daily 顯示 `SOURCE ETH`。
- [ ] 以 2105／2023-12-21 回歸：ETH H1 必須顯示到 47.90 的 K 棒，且 canonical Daily BOS line、Weekly swing value 與 Daily 圖一致；RTH 畫面不得作為跨時框驗收依據。
- [ ] 對每個 Bullish BOS，核對 OB source 是紅線左右端點之間 `low` 最低的 bearish K；Bearish BOS 核對為 `high` 最高的 bullish K。左右端點與 Doji 不可入選。
- [ ] 若出現同低／同高，確認選擇較靠近 BOS 的反向 K；區間無反向 K 時不建立 OB。
- [ ] 回歸 DZONE-07 的紅色 BOS line：左端 pivot K、structure price、右端 BOS K 不得因 source 規則改變；Daily、H4、H1 同一事件必須一致。
- [ ] 確認 W BULL／W BEAR marker 每次 canonical Weekly flip 只出現一次；切換時框或重新載入後目前 Bias 與表格不變。
- [ ] 執行 Daily zones 的 source timestamp／top／bottom、逐區失效日與 Replay/reload exact audit。
- [ ] Weekly Bias 與 Daily zones 都完成 exact reconciliation 後，才進入獨立 Daily MSS Bias；不提前加入 execution。

## 目前穩定基準

- V1 `V1-LONG-01`、V4 `V4-LONG-01`。
- V10 `V10-DZONE-07` 的 pivot-to-BOS 水平線已通過 2324／2634 Daily 視覺確認；目前候選 `V10-DZONE-09` 保留 DZONE-08 的極值反向 K，並統一 canonical ETH feed，待 TradingView 驗證。
- H1／1095D／`FULL`，台股 execution 與統計固定 Long-only。
- V1／V4 Weekly zone 與 V10 Daily FVG 的 midpoint invalidation 保持現行規則；V10 Daily OB 已改為完成 Daily close 穿越 full edge。
- ENTRY retest expiry 15 根 H1；TP1 後剩餘部位 SL 移到 Entry。
- 每個 exact Weekly zone 最多一筆有效 Trade Plan。

## 下一輪不混入

- V4 LEGACY 模型維持關閉。
- V4 保持 V1 舊架構核對層，不同步 V10 Weekly Bias／Daily zones／Daily MSS；新架構統計版待 V10 execution 完成後另建。
- V1／V4 zone invalidation 第二輪評估仍暫緩；本輪 full-edge 修改只套用 V10 Daily OB。
- OB/FVG 分組績效另開獨立工作，不與 zone invalidation 同時修改。
- 不處理自動下單、券商連線或真實部位管理。
- `OFF / TOUCH / FULL` 保留作為故障隔離開關；正式移除或隱藏另開小步驟。
