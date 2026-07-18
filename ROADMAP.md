# Roadmap

Roadmap 只列中長期方向；當前工作以 `TODO.md` 為準。

## 近期

- V10 所有後續 Daily MSS 與 H1 execution 開發均以 ETH 為唯一 session 基準；任何 RTH／ETH 混用都必須先修正，不進入訊號或績效比較。
- `V10-DZONE-05` canonical Weekly table 已通過 2324 Weekly／Daily／H4／H1 對齊，canonical Daily zones 第一輪視覺亦無 regression。
- 先驗證 `V10-DZONE-09` 的 ETH canonical feed、SESSION 警告、extreme opposing OB source 與 Daily/H4/H1 一致性，同時回歸 DZONE-07 水平線座標；再確認 Weekly marker／reload，最後完成 Daily zone exact audit 與 Replay 測試。
- OB/FVG 跨時框通過後，以 `V10-DZONE-04` 為基準加入獨立 Daily MSS Bias；第一版只驗證 Daily／H1 state 與右上表，不加入 execution。
- Daily MSS 通過後，再定義 `Weekly Bias + Daily Bias + Daily zone touch` 如何形成 H1 SETUP；每次只加入一個 execution stage。
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
