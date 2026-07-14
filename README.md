# TradingView SMC Replay Toolkit

台股 SMC 回放與統計工具。專案知識以 Repository 文件為準；未經 TradingView 驗證的功能不得視為完成。

## 目前狀態（2026-07-14）

- 正式研究架構：`Weekly Zone → Daily MSS Bias → H1 SETUP / ARMED / ENTRY`。
- V1 穩定基準：`V1-PZ-01`，H1 使用 `PZ OFF`。
- V4 穩定基準：`V4-PZ-02`，H1 使用 `PZ OFF`。
- 1504、2105、2324 已確認 V1/V4 在 H1 可同時顯示。
- per-zone SETUP engine 尚未完成；下一步只測 V1 `TOUCH`，不先修改 V4。

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

## 主要程式

- `smc-weekly-ob-fvg/assets/smc_weekly_ob_fvg_v1.pine`：完整繪圖、交易流程與詳細 funnel。
- `smc-weekly-ob-fvg/assets/smc_top_down_models_v4.pine`：W-D-H1 PRIMARY 統計核對；LEGACY engines 目前關閉。
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
