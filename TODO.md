# TODO

本檔只保留下一個可執行工作。已完成證據放在 `TEST_RESULT.md`，歷史決策放在 `PROJECT_HISTORY.md`。

## 下一步：補齊 V10-DH1-ENTRY-03 特殊路徑回歸

- [x] 2317／2105 ETH H1 compile／runtime、BUILD、SESSION、OB+FVG各階段與NET R來源加總首輪通過；2317仍有3個ARMED ACTIVE未成交，符合pending Buy Limit。
- [ ] 用Replay讓尚未成交的Buy Limit遇到Weekly Bias改變，確認pending line停止且不建立ENTRY。
- [ ] 找到ENTRY K同時包含SL與TP的案例，確認只計DIRECT SL、SAME BAR增加且marker為紫紅色。
- [ ] Reload並重跑Replay，確認SETUP／ARMED／ENTRY／結果與來源統計不重複累加；再以2324或2634做一檔回歸。

## 目前穩定基準

- V1 `V1-LONG-01`、V4 `V4-LONG-01`。
- V10 SETUP 基準為 `V10-DH1-SETUP-02R1`；其 Daily zones、First-touch-only SETUP 與 canonical BOS event 去重已完成 2324／ETH H1 視覺驗收。FVG-03、canonical Weekly table與既有 Daily zones 結論保持。
- V10 ARM 基準為 `V10-DH1-ARMED-03`：ARMED-02 lifecycle 已完成 2324／2634／2317 ETH H1 第一輪視覺檢查，break diagnostic 已在 2317／ETH H1 通過。ARMED-01 的 zone-exit lifecycle 已淘汰。
- H1／1095D／`FULL`，台股 execution 與統計固定 Long-only。
- V1／V4 Weekly zone 與 V10 Daily FVG 的 midpoint invalidation 保持現行規則；V10 Daily OB 已改為完成 Daily close 穿越 full edge。
- V1／V4 ENTRY retest expiry仍為15根H1；V10 midpoint Buy Limit不使用expiry，只有Weekly Bias改變撤單。兩者TP1後剩餘部位SL都移到Entry。
- 每個 exact Weekly zone 最多一筆有效 Trade Plan。

## 下一輪不混入

- V4 LEGACY 模型維持關閉。
- V4 保持 V1 舊架構核對層，不同步 V10 Weekly Bias／Daily zones／SETUP；新架構統計版待 V10 execution 完成後另建。
- 不在 ENTRY-01 驗證期間混入 FVG/OB、SETUP或 pre-ARM candidate lifecycle 調整。
- V1／V4 zone invalidation 第二輪評估仍暫緩；本輪 full-edge 修改只套用 V10 Daily OB。
- OB/FVG 分組績效另開獨立工作，不與 zone invalidation 同時修改。
- 不處理自動下單、券商連線或真實部位管理。
- `OFF / TOUCH / FULL` 保留作為故障隔離開關；正式移除或隱藏另開小步驟。
