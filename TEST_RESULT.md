# Test Results

## 2026-07-11 Trade statistics and compare v2

- 使用者截圖確認 V1 在 H4/H1/M30 可顯示 Total、Open、Win TP2、TP1→Loss、Direct Loss、TP1/TP2 Rate、Net R、Avg R、Profit Factor 與訊號漏斗。
- 使用者截圖確認 V2 在 M30 圖表顯示 H4/H1/M30 三列 Compare 表，包含 SETUP、ARMED、Trades、TP1%、TP2%、Net R、Avg R 與 PF；表格位置與 tiny 字體亦有圖表證據。
- 使用者以多個標的取得 H1 730D 統計，證明固定 Window 能讓不同標的採一致期間；這是功能顯示紀錄，不構成策略有效性或勝率驗證。
- Pine Editor 實際回報固定 M30 snapshot 嘗試錯誤：`built-in 'line.set_x2' cannot be used with any parameter of the security() function`；該程式路徑已移除。
- 本次收尾執行 `git diff --check` 與 Pine 靜態內容檢查；結果另記於 commit 前驗證輸出。
- 未實際測試：V2 三列逐筆等同三次 V1、非 M30 圖表固定結果、長時間 Replay 壓力、所有市場/session、最大 arrays/request/object 邊界。因此不標記為通過。

## 2026-07-10 Entry workflow

- 使用者於 TradingView 圖表／Replay 截圖確認過中間版本可顯示 SETUP、ARMED、ENTRY，ARMED 會暗化對應 SETUP，且封存修正後已完成流程的 SETUP 不再因同 zone 新訊號消失。
- Pine Editor 曾回報 box handle 無法使用 `==`；後續版本改用 OB/FVG string key，使用者可再次顯示 ARMED/ENTRY，未再回報該編譯錯誤。
- 本次收尾已執行 Repository 靜態檢查：輸入與物件上限、zone/label/trade-plan 平行 arrays 的 push/shift、zone key 查找與封存、SETUP→ARMED→ENTRY 狀態清除、ENTRY 下一根 K 起算、SL 優先、TP1/TP2/LOSS 狀態，以及非法 SL/TP2 下限防護。
- 本次收尾已執行 `git diff --check`；沒有 whitespace error。
- 尚未驗證：加入最終 Trade Plan 後的 Pine Editor compile、SL/TP 圖表顯示與結果；Daily/H4/M15 完整一致性矩陣；所有取消條件、標籤／物件上限與長時間 Replay 壓力測試。因此這些項目不標記為通過。

## 2026-07-10 Repository 文件整理

- 狀態：完成靜態檢查，未在本次工作中執行 TradingView Pine Editor compile 或圖表視覺驗證。
- 已檢查：文件規格與目前 `smc_weekly_ob_fvg_v1.pine` 的 inputs、Weekly/Daily 聚合、OB/FVG、CHOCH/MSS、midpoint invalidation、物件 trimming 邏輯相符。
- 尚待人工驗證：TradingView Daily、H4、M15 顯示一致性與 Bar Replay 長時間逐 bar 行為。

## 既有測試紀錄摘要

依 `PROJECT_HISTORY.md`，開發期間曾針對 Replay 顯示、物件壓力、OB 重複來源、Daily CHOCH/MSS 跨 timeframe 顯示進行多輪修正。現有 Repository 沒有可自動執行的 Pine 測試套件，也沒有保存足以重現每輪結果的完整測試矩陣，因此不將這些歷史修正標記為全面通過。

## 建議測試矩陣

| 項目 | Daily | H4 | M15 | Replay |
|---|---:|---:|---:|---:|
| Weekly OB/FVG 位置一致 | 待測 | 待測 | 待測 | 待測 |
| Daily CHOCH/MSS 事件一致 | 基準 | 待測 | 待測 | 待測 |
| midpoint invalidation 終止位置 | 待測 | 待測 | 待測 | 待測 |
| 切換 timeframe 後物件保留 | 待測 | 待測 | 待測 | 待測 |
| 達到物件上限時刪除最舊項目 | 待測 | 待測 | 待測 | 待測 |
