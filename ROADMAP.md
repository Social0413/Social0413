# Roadmap

Roadmap 只列中長期方向；當前工作以 `TODO.md` 為準。

## 近期

- V10 所有後續 H1 execution 開發均以 ETH 為唯一 session 基準；任何 RTH／ETH 混用都必須先修正，不進入訊號或績效比較。
- `V10-DZONE-05` canonical Weekly table 已通過 2324 Weekly／Daily／H4／H1 對齊，canonical Daily zones 第一輪視覺亦無 regression。
- `V10-FVG-03` 的 isolated `0.50 ATR + 2 ticks` minimum gap 已通過 2105／Daily 視覺過濾；其餘 K1/K2/K3 metadata、canonical Daily close-time endpoint、DZONE-09 ETH feed、DZONE-08 extreme opposing OB source、DZONE-07 水平線及 Daily/H4/H1 exact audit 改列既知回歸，不在下一個 SETUP 任務中混改 zone 定義。
- `V10-DH1-SETUP-02R1` 已在 2324／ETH H1 通過 compile／runtime、SETUP `24 / 0 ACTIVE` 與 canonical BOS one-event-one-object 視覺驗收，作為 V10 SETUP 基準；First-touch、lower-low、re-entry block、reload／Replay 保留為後續回歸項。
- V10已凍結為研究基準`V10-BASELINE-01`，行為與ENTRY-05相同。下一步先用相同ETH H1／1825D FULL／Replay終點完成第二批15檔未見樣本，不調整規則；Window邊界、PART、hard invalidation、same-bar及reload／Replay仍列為補充驗證。
- 以最終 `V1-LONG-01`／`V4-LONG-01` 在 2376/H1 建立 current-build 回歸基準。
- 若使用者重啟 zone invalidation 議題，先只在 V1 比較 midpoint 與 full-edge close invalidation，不與其他策略規則同時修改。
- 建立 OB/FVG 分組交易統計。

## 中期

- 用 V3 對多檔台股進行三年跨週期統計。
- 分析跨股票一致性、移除最佳單一股票後的結果與參數穩健度。
- 將訊號判定與繪圖管理進一步分離，降低回歸風險。
- V10 execution 規格與視覺驗證完成後，另建新架構統計核對版本；不覆蓋 V4。
- 策略凍結後才進行 paper trade／極小部位驗證。

## 暫不納入

- 自動下單或券商連線。
- 績效保證。
- 未經規格確認的其他平台移植。
