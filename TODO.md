# TODO

本檔只保留下一個可執行工作。已完成證據放在 `TEST_RESULT.md`，歷史決策放在 `PROJECT_HISTORY.md`。

## 下一步：V10 Baseline第二批15檔未見樣本

- [ ] 先在任一ETH H1圖載入`V10-BASELINE-01`，確認runtime、右上BUILD、SESSION及execution統計正常；6669／Daily已確認BUILD與`SOURCE ETH`，但Daily的`USE H1`不取代本項H1回歸。預期交易行為與ENTRY-05相同。
- [ ] 隨機選擇未出現在首批的15檔；固定ETH H1、`WINDOW 1825D FULL`、Replay TO `2026-07-17`及FROM `2021-07-18`。任何`PART`標的移出直接比較並另換一檔FULL。
- [ ] 每檔保存右上全域與OB／FVG來源表，至少包含SETUP、ARMED、ENTRY、Open、TP1 HIT、TP2、TP1→BE、Direct SL與Net R；OB＋FVG各列必須等於全域。
- [ ] 第二批單獨統計後，再與首批合併為30檔；同時保留「首批／第二批」分組，觀察平均R、OB／FVG差異及正負標的分布是否重現。
- [ ] 第二批完成前不修改SETUP、ARMED、ENTRY、Pending、TP／SL、OB／FVG或Window規則，也不依單一標的結果調參。

## 目前穩定基準

- V1 `V1-LONG-01`、V4 `V4-LONG-01`。
- V10 SETUP 基準為 `V10-DH1-SETUP-02R1`；其 Daily zones、First-touch-only SETUP 與 canonical BOS event 去重已完成 2324／ETH H1 視覺驗收。FVG-03、canonical Weekly table與既有 Daily zones 結論保持。
- V10 ARM 基準為 `V10-DH1-ARMED-03`：ARMED-02 lifecycle 已完成 2324／2634／2317 ETH H1 第一輪視覺檢查，break diagnostic 已在 2317／ETH H1 通過。ARMED-01 的 zone-exit lifecycle 已淘汰。
- V10研究基準為`V10-BASELINE-01`；行為與ENTRY-05相同，固定ETH H1／1825D，只有`FULL`標的可直接跨標的比較。首批15檔是探索樣本，下一批15檔是未見樣本。
- V1／V4仍固定H1／1095D／`FULL`；V10改為H1／1825D／`FULL`。台股execution與統計全部固定Long-only。
- V1／V4 Weekly zone 與 V10 Daily FVG 的 midpoint invalidation 保持現行規則；V10 Daily OB 已改為完成 Daily close 穿越 full edge。
- V1／V4 ENTRY retest expiry仍為15根H1；V10 midpoint Buy Limit不使用expiry，只有Weekly Bias改變撤單。兩者TP1後剩餘部位SL都移到Entry。
- 每個 exact Weekly zone 最多一筆有效 Trade Plan。

## 下一輪不混入

- V4 LEGACY 模型維持關閉。
- V4 保持 V1 舊架構核對層，不同步 V10 Weekly Bias／Daily zones／SETUP；新架構統計版待 V10 execution 完成後另建。
- 不在 ENTRY-01 驗證期間混入 FVG/OB、SETUP或 pre-ARM candidate lifecycle 調整。
- V1／V4 zone invalidation 第二輪評估仍暫緩；本輪 full-edge 修改只套用 V10 Daily OB。
- OB/FVG 分組績效另開獨立工作，不與 zone invalidation 同時修改。
- 第二批15檔完成前不加入手續費模型、MFE／MAE、Pending age filter或任何新策略條件；這些只能在Baseline原始樣本保存後另開研究版本。
- 不處理自動下單、券商連線或真實部位管理。
- `OFF / TOUCH / FULL` 保留作為故障隔離開關；正式移除或隱藏另開小步驟。
