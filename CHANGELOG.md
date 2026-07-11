# Changelog

本文件依現有 Git history 與 `PROJECT_HISTORY.md` 整理；早期版本未使用正式 release tag。

## Unreleased

- V1 新增可選 H4/H1/M30 Entry timeframe、固定統計 Window、24 小時 SETUP expiry 換算、TP1 分批 R 模型、永久累計績效表與訊號漏斗；詳細表改為 tiny 字體。
- 新增 `smc_weekly_ob_fvg_compare_v2.pine`：在 M30 圖表內平行維護 H4/H1/M30 三套狀態，右下角顯示三週期 SETUP、ARMED、Trades、TP1%、TP2%、Net R、Avg R 與 PF 比較表。
- V2 預設關閉單週期 SETUP/ARMED/ENTRY、Trade Plan 與詳細統計顯示，避免和 Compare 表重疊；Weekly OB/FVG 與 Daily structure 繪圖仍保留。
- 回退不可用的固定 M30 `security()` snapshot 嘗試；目前非 M30 圖表顯示 `USE M30 CHART`，跨圖表週期固定結果列為後續工作。

- 新增 `SMC_SPEC.md`、`DESIGN.md`、`ROADMAP.md`、`CODING_RULE.md`、`TEST_RESULT.md`、`KNOWN_BUGS.md`、`TODO.md`，並建立 Repository 知識索引。
- 新增完整進場工作流：Daily MSS bias 與 Weekly zone 形成 SETUP；圖表時框 pivot breakout 加 ATR displacement 形成 ARMED；首次回踩突破位並收盤確認形成 ENTRY。
- SETUP/ARMED/ENTRY 各最多 40 個標籤；SETUP 以 zone key 管理，重進同 zone 只替換尚未 ARMED 的標籤，ARMED 後暗化並封存以保留流程鏈。
- 新增 Trade Plan：ENTRY 收盤、保護 swing SL、預設 1R/2R 目標、下一根 K 起算、同 K 採 SL 優先，以及最多 20 筆整組裁切；僅供圖表分析，不送單。
- ENTRY 保護 swing 改為收盤突破才失效；retest expiry 預設 0（關閉）。OB/FVG 重疊時依距收盤最近 midpoint 選擇 zone，zone key 改變視為新 SETUP。
- CHOCH 隱藏文字但保留結構線；MSS 線與文字維持顯示。ARMED zone 追蹤使用字串 key，避免 Pine 不支援 box handle equality 的編譯錯誤。

## 2026-07-10

- 將 CHOCH/MSS 改為由被突破 Daily pivot 到 breakout candle 的固定結構線段。
- Intraday chart 改由完成的 Daily candle 聚合執行 CHOCH/MSS，移除不穩定的 `request.security("D")` 顯示路徑。
- 恢復並調整 Daily CHOCH/MSS，加入物件裁切與觸碰處理的迭代。

## 既有功能演進

- 建立 Weekly OB/FVG Pine 指標與 TradingView 開啟、關閉、指定 symbol 工具。
- 改善 Replay 延伸行為、midpoint invalidation 與資源上限。
- OB 改為 structure-break 規則，並防止同一來源 candle 重複建立。
- 新增後再移除 365D High/Low；移除原因為 Replay 資源壓力。
- 新增 Daily CHOCH/MSS，參考 `03_H4M15` 邏輯後逐步修正跨 timeframe 顯示。
