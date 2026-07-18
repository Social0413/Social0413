# TradingView SMC Replay Toolkit

台股 SMC 回放與統計工具。專案知識以 Repository 文件為準；未經 TradingView 驗證的功能不得視為完成。

## 目前狀態（2026-07-18）

- V10 新研究架構：`Weekly Structure Bias → Daily OB/FVG + Daily MSS → H1 SETUP / ARMED / ENTRY`；Weekly 只管方向，Daily 管位置與結構，H1 管進場。
- 目前穩定版為 V1 `V1-LONG-01` 與 V4 `V4-LONG-01`；正式入口固定 H1、1095D、`FULL`，execution 與全部績效只做多。
- 新架構已由獨立 V10 開始開發；canonical Weekly table 與第一輪 Daily zones 已完成跨時框回歸，`V10-DZONE-07` 的紅色 pivot-to-BOS 水平線亦通過 2324／2634 Daily 視覺確認。目前候選 `V10-DZONE-09` 保留 DZONE-08 的極值反向 OB source，並將 canonical Weekly／Daily requests 固定為 ETH；execution 仍未加入。
- V10 的開發、Replay 與未來 H1 execution 一律使用 ETH。程式內 canonical feed 由 `ticker.modify(..., session.extended)`強制統一；Pine 無法替使用者切換原生圖表 session，因此 H1／H4 圖表若仍為 RTH，右上 SESSION 必須顯示 `USE ETH (...)`，不可用該畫面驗收跨時框價格。
- Bearish Weekly OB/FVG 與 Daily bearish MSS/CHOCH 繼續顯示作為風險 context，但不建立 S SETUP／ARMED／ENTRY／Trade，也不進入統計；Bearish OB/FVG 使用兩階淺紅色。
- 每個確切 Weekly zone 最多一筆有效 Trade Plan；ENTRY retest expiry 預設 15 根 H1，TP1 後剩餘部位 SL 移到 Entry，預設 TP1→BE 為 +0.5R。
- V1/V4 的完整／COMPACT 統計顯示均已驗證；COMPACT 只保留交易績效，不改底層計算。
- 2105、2324/H1／1095D／FULL 的 V1/V4 SETUP、replacement、ARMED、Total、績效、OB/FVG 與 Same/Changed 已全部對齊。
- Weekly zone 目前仍以 H1 收盤穿越 midpoint 失效；「收盤穿越 zone edge 才失效」只完成影響評估，尚未實作。
- 2376 尚未用最終 `LONG-01` 做一次完整 V1/V4 回歸；這是下一輪安全基準，不是目前阻斷問題。
- V1 是視覺檢查層、V4 是統計核對層；兩者的 Weekly Zone、Daily Bias、Window、SETUP／ARMED／ENTRY、失效與績效核心邏輯必須相同，只有顯示方式可以不同。
- 核心修改固定先在 V1 完成 TradingView 驗證，再原樣移植到 V4 並核對統計；不得讓兩支程式各自演化成不同策略。

## 建議閱讀順序

日常開發只需依序閱讀：

1. [SMC_SPEC.md](SMC_SPEC.md)：策略規格與目標行為。
2. [DESIGN.md](DESIGN.md)：資料流與程式架構。
3. [TODO.md](TODO.md)：目前唯一的執行清單。

遇到問題時再閱讀：

1. [KNOWN_BUGS.md](KNOWN_BUGS.md)：現行問題、已排除項目與禁止重試方案。
2. [TEST_RESULT.md](TEST_RESULT.md)：實際 TradingView 驗證證據。
3. [PROJECT_HISTORY.md](PROJECT_HISTORY.md)：重大決策、錯誤、rollback 與歷史。

其他文件：

- [CHANGELOG.md](CHANGELOG.md)：真正保留下來的程式變更。
- [ROADMAP.md](ROADMAP.md)：中長期方向，不是當前待辦。
- [CODING_RULE.md](CODING_RULE.md)：Pine 與版本控制規則。
- [CLOSEOUT_CHECKLIST.md](CLOSEOUT_CHECKLIST.md)：每段對話固定收尾流程。

## 開發與驗證分工

- Codex 負責依 Repository 規格直接修改 Pine 檔，並完成可在本機執行的靜態檢查。
- 使用者負責在 TradingView Pine Editor compile、載入圖表及實圖驗證，並將結果回報給 Codex。
- 除非使用者明確要求，Codex 不開啟或操作 TradingView，也不代替使用者執行圖表測試。
- 使用者尚未回報 TradingView 結果前，任何修改只能標記為「待 TradingView 驗證」，不得宣稱通過。

## 主要程式

- `smc-weekly-ob-fvg/assets/smc_weekly_ob_fvg_v1.pine`：完整繪圖、交易流程與詳細 funnel。
- `smc-weekly-ob-fvg/assets/smc_top_down_models_v4.pine`：W-D-H1 PRIMARY 統計核對；LEGACY engines 目前關閉。
- `smc-weekly-ob-fvg/assets/smc_weekly_structure_bias_v10.pine`：V10 新架構；目前為 Weekly Bias + Daily OB/FVG，右上永久顯示 build ID。
- `smc-weekly-ob-fvg/assets/smc_weekly_ob_fvg_cross_tf_v3.pine`：M30/H1/H4 長期跨週期統計研究。
- `smc-weekly-ob-fvg/assets/smc_weekly_ob_fvg_compare_v2.pine`：舊 M30 compare 工具，非目前 SETUP 主線。

## 文件責任

- Spec 寫「應該怎麼運作」。
- Design 寫「程式如何實作」。
- TODO 只寫「下一步要做什麼」。
- Test Result 只寫「實際驗證證據」。
- Known Bugs 只寫「尚未解決的問題」。
- Project History 保存重要歷史，日常不必全部閱讀。
- Changelog 不記錄已撤回成現況的功能。

## 收尾

每次功能或除錯階段結束時，依 [CLOSEOUT_CHECKLIST.md](CLOSEOUT_CHECKLIST.md) 更新錯誤、學習、驗證、版本與下一步。只有使用者要求時才 commit／push。
