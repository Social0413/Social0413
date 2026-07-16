# TODO

本檔只保留下一個可執行工作。已完成證據放在 `TEST_RESULT.md`，歷史決策放在 `PROJECT_HISTORY.md`。

## 下一步：Zone invalidation 第二輪評估

- [ ] 先以最終 `V1-LONG-01`／`V4-LONG-01` 在 2376/H1／1095D／FULL 記錄 current-build 共通統計，作為修改前基準。
- [ ] 重新確認是否真的要把 Weekly zone 失效由 H1 close 穿越 midpoint 改為穿越完整 zone edge；未選定前不修改程式。
- [ ] 若選定 full-edge close invalidation，只建立 V1 候選版：Bullish close < bottom、Bearish close > top；影線穿越不失效。
- [ ] 用 2376、2324、2105、2609 比較 active zone 重疊、SETUP、ARMED、Total、失效分類與績效；V1 通過後才同步 V4。

## 目前穩定基準

- V1 `V1-LONG-01`、V4 `V4-LONG-01`。
- H1／1095D／`FULL`，台股 execution 與統計固定 Long-only。
- Zone midpoint invalidation 保持現行規則。
- ENTRY retest expiry 15 根 H1；TP1 後剩餘部位 SL 移到 Entry。
- 每個 exact Weekly zone 最多一筆有效 Trade Plan。

## 下一輪不混入

- V4 LEGACY 模型維持關閉。
- OB/FVG 分組績效另開獨立工作，不與 zone invalidation 同時修改。
- 不處理自動下單、券商連線或真實部位管理。
- `OFF / TOUCH / FULL` 保留作為故障隔離開關；正式移除或隱藏另開小步驟。
