# Changelog

## 2026-07-14 V1-PZ-01 / V4-PZ-02 stable diagnostic baseline

- Added visible build IDs to both indicator names and result-table headers.
- Added V1 `OFF / TOUCH / FULL` per-zone diagnostic stages to isolate the H1 blank-output problem.
- Defaulted V1 diagnostic mode to `OFF`, so Weekly zones and the stats table can be verified before enabling the new SETUP engine.
- Follow-up `V4-PZ-02` adds an `OFF / FULL` engine switch, default `OFF`, after V4 was confirmed to disappear only on H1.
- Verified 1504、2105、2324 on H1 with V1/V4 loaded together in `PZ OFF`; Weekly zones and both tables render normally.
- Rolled back the unverified `V1-PZ-02 / V4-PZ-03` optimization after `FULL` still caused blank H1 output.
- Reorganized Repository MD ownership and added `CLOSEOUT_CHECKLIST.md`; current status, target specification, tests, bugs and next actions are now explicitly separated.

## Unreleased（目標已實作成草稿，但尚未通過 H1 驗證）

- V1/V4 PRIMARY 曾加入 per-zone 獨立流程草稿，但 H1 `FULL` 會停止顯示，目前以 diagnostic `OFF` 作為穩定基準。此項不得視為已完成；目標仍為每個 Weekly OB/FVG 各自形成 SETUP、ARMED、ENTRY 與 Trade Plan。
- V4 PRIMARY 執行入口由 H4 data carrier 改為 H1 chart，直接使用圖表 H1 bars；移除 PRIMARY 的 H1 lower-timeframe replay，並停止執行兩個 LEGACY 模型，使 V1/V4 可在同一 H1 畫面比較。H1 直接執行版已用 2105、1504、2324 驗證所有可比欄位一致。
- 台股主要模型由 `W-D-H4` 改為 `W-D-H1`：V1 只在 H1 chart 建立 SETUP／ARMED／ENTRY；其他圖表顯示 `Use H1 chart`。
- V4 第一列改為 `PRIMARY W-D-H1`，在 H4 data-carrier chart 逐根回放 H1 arrays 執行 Weekly zone、Daily Bias 與 H1 execution；原另外兩列標記為 LEGACY。新版 V1/V4 已在 2105、1504、2324 完成所有可比欄位一致性驗證。
- V1 的 Daily CHOCH/MSS 結構線與文字改為只在 Daily chart 繪製；H4/H1/M30 仍由完成的 Daily candles 更新結構狀態與 SETUP Bias，策略判定不變。
- 將 V1 與 V4 SWING 的 Daily MSS 預設 swing length 由 5 改為 4；MSS 成立時固定保存反方向 confirmed Daily pivot，完成 Daily close 穿越後 Bias 轉為 Neutral。失效位不 trailing，也不使用 CHOCH 或時間期限取消 Bias。
- 再次收斂 V1 與 V4 的 FVG 規則：保留標準三根完成 K 的 wick-to-wick gap，以及同方向、body 至少為來源時框 Wilder ATR(14) × 1.0 的中間 displacement；移除過嚴的 ATR × 0.5 gap 寬度與確認 K 順向半部條件。現有 midpoint invalidation 與其他交易流程不變；已在 TradingView H4／1095D 以 2105、1504、2324 完成 V1／V4 SWING 所有可比欄位回歸，三者完全一致。
- 收斂 V1 與 V4 的 OB 規則：結構突破 candle body 必須至少為來源時框 Wilder ATR(14) × 1.0，才回找 displacement 前最近的反向 candle；OB 範圍固定改為 Hybrid Range，移除 Wick／Body 輸入。V1 使用 Weekly ATR，V4 Weekly／Daily／H4 Zone Engine 分別使用各自來源時框 ATR；已在 TradingView H4／1095D 以 2105、1504、2324 完成 V1／V4 SWING 所有可比欄位回歸，三者完全一致。
- 新增獨立 V4 Top-down Model Research Engine 開發規格；保留 V3 不覆蓋。
- V4 將比較 `W→D→H4`、`D→H4→H1`、`H4→H1→M30` 三套完整模型，並拆分 Raw/Unique/Replacement funnel。
- 將 V4 Weekly／Daily／H4 Zone Engine 由單一最新 Zone 升級為與 V1 規則一致的多 Zone arrays；OB/FVG 每類最多 40 個，使用相同 structure breakout、opposite-candle searchback、三 candle FVG、midpoint invalidation 與最近 midpoint touch 選擇。
- 對齊 V4 SWING 與 V1 的 Daily MSS bias：由 H4 bars 聚合完成 Daily candles，使用相同 confirmed pivot、True Range 平均、ATR displacement 與 reversal-only bias 更新。
- 對齊 V1/V4 的 1095D Window 起點 touch state與 OB-first/FVG-second Zone 選擇順序；V4 新增 S-RPL、A-RPL、OB、FVG、SAME、CHG 診斷欄供逐欄核對。
- 修正 V4 array pivot 的同高／同低優先規則，並將 bullish／bearish previous-zone state 分開保存，使 SETUP event 邊界與 V1 相同。
- V4 統計欄位改用 V1 名稱；2105、1504、2324 的 V1 與 V4 `SWING W-D-H4` 所有可比欄位已在 TradingView H4／1095D 實圖逐欄一致。
- 新增三引擎架構圖，並建立 W-D-H4 五階段微調待辦：Weekly Zone、Daily MSS、SETUP、ARMED、ENTRY；本階段暫不調整 SL／TP。

本文件依現有 Git history 與 `PROJECT_HISTORY.md` 整理；早期版本未使用正式 release tag。

## Prior development history

- V3 長期研究 Window 改為 1095D／1825D／2555D；以 TradingView Essential 與台股為資源基準，5 年與 7 年要求在 H4 chart 執行，並以 `calc_bars_count=100000` 取得 M30/H1 intrabars。
- V3 固定關閉 Weekly zone、Daily structure、SETUP/ARMED/ENTRY 與 Trade Plan 繪圖，只保留純數值狀態與 H4/H1/M30 統計表；V1 保留為單引擎交易細節檢查工具。
- V3 分別追蹤 M30、H1、H4 資料起點，逐列顯示 `FULL`／`PART`；三列皆涵蓋統計起點時才顯示 `3TF V3 FULL`。
- 使用者已在 TWSE:2317 H4 chart 完成實圖檢查：1825D 與 2555D 均顯示 H4/H1/M30 `FULL`。此結果只證明該 symbol 與本次環境的資料覆蓋，不代表其他台股均有相同 intraday 歷史。
- 策略研究方向調整為最近三年、多檔流動性與交易特性相近的台股、同一套規則與參數；V3 用於三引擎及跨股票一致性比較，V1 用於好／中／差交易的抽樣稽核。5 年／7 年保留為輔助穩健性觀察，不作為近期主要調參資料。

- V1 移除手動 `Entry timeframe` 選項；H4、H1、M30 chart 自動以目前圖表週期作為 Entry timeframe，其他週期不建立新候選並顯示切換提示。
- V1 將 Trade Plan 計算與顯示解耦；關閉 `Show SL/TP trade plans` 只隱藏 lines/labels，不再停止交易與績效統計。
- 新增獨立 `smc_weekly_ob_fvg_cross_tf_v3.pine`：以完成 M30 bars 作為基礎資料流，維護 M30/H1/H4 三套獨立狀態；H1/H4 chart 使用 lower-timeframe arrays 回放，表格固定顯示 H4、H1、M30 三列。
- V3 將 chart-side pivot 與 ATR series 保持在全域逐次計算，避免條件迴圈內呼叫 `ta.pivothigh()`、`ta.pivotlow()`、`ta.atr()` 的 consistency 警告。
- V3 已完成 Pine Editor compile，並以同一 symbol、365D Window 驗證 M30/H1/H4 圖表的三列統計一致；其他市場、Window 與資源邊界仍列為未測。
- V1 新增 H4/H1/M30 chart-driven Entry timeframe、固定統計 Window、24 小時 SETUP expiry 換算、TP1 分批 R 模型、永久累計績效表與訊號漏斗；詳細表改為 tiny 字體。
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
