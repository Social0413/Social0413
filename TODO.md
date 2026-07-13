# TODO

- [x] Recompile the reconciled V4 in TradingView and confirm 2105, 1504 and 2324 match V1 field by field.

## W-D-H4 策略微調主待辦

> 以 V1 作為完整繪圖與逐筆檢查工具，以 V4 `SWING W-D-H4` 作為統計核對引擎。以下五項後續分成獨立對話，依順序逐項討論、修改及回歸測試；本階段先不調整 SL／TP。

- [ ] 1. Weekly Zone：檢查與微調 Weekly OB／FVG 的建立、篩選、寬度、有效性及 midpoint invalidation。
  - [x] V1／V4 同步加入來源時框 Wilder ATR(14) × 1.0 OB displacement，並固定 Hybrid Range。
  - [x] Pine Editor compile，並以 2105、1504、2324 核對新版 OB 圖形及 V1／V4 SWING 所有可比欄位；三個標的均一致。
  - [x] V1／V4 同步將 FVG 收斂為標準 wick-to-wick gap，加上同方向 1 ATR 中間 displacement；移除 gap 寬度與確認 K 額外條件。
  - [x] Pine Editor compile，並以 2105、1504、2324 重新核對現行 FVG 圖形及 V1／V4 SWING 所有可比欄位；三個標的均一致。
- [ ] 2. Daily MSS：檢查與微調 Daily Pivot、MSS、ATR displacement 與多空 Bias 的敏感度及延遲。
- [ ] 3. SETUP：檢查與微調 Daily Bias 配合 Weekly Zone、Zone touch、重入、替換及有效期限。
- [ ] 4. ARMED：檢查與微調 H4 Pivot 突破、ATR displacement、保護條件及失效原因。
- [ ] 5. ENTRY：檢查與微調 ARMED 後的 H4 回測確認、等待期限及進場失效條件。

每一項完成條件：同步修改 V1 與 V4 SWING、通過 Pine Editor compile，並以固定案例確認兩邊所有可比欄位完全一致後，才進入下一項。

- [x] 新增獨立 V4 原型，不覆蓋 V3；比較 `W→D→H4`、`D→H4→H1`、`H4→H1→M30` 三套 Top-down models。
- [x] 在 TradingView H4 chart compile V4，修正 Pine syntax／request limits，確認三列都有資料。
- [ ] 驗證 V4 Weekly/Daily/H4 zone 與 Daily/H4/H1 bias 僅在 completed candle 後生效，沒有 intrabar lookahead。
- [ ] 逐筆核對 Raw SETUP、Unique SETUP、Replaced、ARMED、TRADES 與 U>A／A>T conversion。
- [x] 多 Zone V4 compile 後，以 2105、1504、2324 同一 H4/1095D 畫面逐欄比較 V1 與 V4 SWING，所有可比統計完全一致。

## 長期研究模式（已確認方向）

- [x] 曾為 V3 增加 1095D／1825D／2555D；後續依最新決定將 V1/V3 強制固定為 1095D。
- [x] 將 V3 改為純統計用途，取消所有非必要 box、line、label 與單週期 Trade Plan 繪圖；保留 V1 作為單引擎交易細節檢查工具。尚待 Pine Editor 驗收。
- [x] 1095D 可在資料完整時使用 M30 chart；1825D／2555D V3 以 H4 chart 為標準執行入口，從 H4 chart 取得 M30/H1 intrabars。
- [ ] 顯示 requested Window start、實際第一根 M30/H1/H4 時間與 warm-up 狀態；三列獨立 FULL/PARTIAL 判斷已完成，日期與 warm-up 顯示仍待開發。
- [ ] 以多個台股在 Pine Editor 實測固定 1095D 的 compile、執行時間、記憶體、coverage 與 V1 抽樣一致性。
- [ ] 建立固定的三年台股研究股票池與納入條件（流動性、資料完整性、交易特性）；所有股票使用同一套規則與參數。
- [ ] 建立跨股票彙總紀錄：每檔 H4/H1/M30 Trades、Net R、Avg R、PF、正負 Avg R 股票數，以及移除最佳單一股票後的結果。
- [ ] 使用 V1 抽查表現好／中／差的股票與 Win/Loss 交易；只有跨多檔股票重複且有市場理由的問題才提出規則修改。
- [ ] 每次規則修改後以 V3 重跑完整股票池，避免只改善觸發修改的單一股票。
- [ ] 策略與參數凍結後，先以 paper trade 或極小部位累積實際交易，記錄成交、滑價與模擬差異，再決定是否增加部位。

- [x] 新增 V1 進場後統計表、R 模型與訊號漏斗。
- [x] 新增固定統計 Window 與按小時換算的 SETUP expiry。
- [x] 新增 V2 M30 圖表內的 H4/H1/M30 自動比較表。
- [ ] 逐筆比對 V2 三列結果與相同 symbol/window 下三次 V1 統計。
- [x] 完成獨立 V3 coding：以 M30 intrabar arrays 在 H1/H4 圖表回放 M30 基礎資料流，並維護 M30/H1/H4 三套統計狀態；不把 line/label/box 帶入 `security()`。
- [x] 在 Pine Editor compile V3，修正 Pine syntax 與 consistency 問題。
- [x] 以相同 symbol、365D Window 逐欄比對 V3 在三種圖表上的三列 SETUP、ARMED、Trades、TP1%、TP2%、Net R、Avg R、PF。
- [ ] 若 V3 三週期不一致，將 Weekly zone active state 與 Daily bias 完全移入純 M30 數值核心後再驗收。
- [ ] 測試 V2 長時間 Replay、交易 arrays 與 Pine request/object limits。

- [ ] 在 TradingView Pine Editor compile 現行 script，記錄版本與結果。
- [ ] 以相同 symbol/期間比對 Daily、H4、M15 的 Weekly OB/FVG。
- [ ] 比對 Daily 基準與 H4/M15 聚合產生的 CHOCH/MSS。
- [ ] 測試 Bullish/Bearish OB 與 FVG 的 midpoint invalidation。
- [ ] 以 Bar Replay 逐 bar 驗證區域建立、停止延伸與 timeframe 切換。
- [ ] 測試 `Maximum zones per type` 與 `Maximum CHOCH/MSS lines` 邊界。
- [ ] 決定是否將 `High timeframe` 固定為 Weekly。
- [ ] 每次功能變更同步維護規格、Changelog、測試結果與已知限制。
- [ ] 在 Daily、H4、M15 驗證 `B SETUP`、`S SETUP` 是否只在同方向 Daily MSS bias 與有效 Weekly zone 重疊時出現。
- [ ] 驗證同一次連續停留在 zone 內只顯示一次，離開再進入時重新顯示。
- [ ] 驗證 `Maximum SETUP labels` 在最小值、預設 40 與最大值時均只刪除最舊標籤。
- [ ] 驗證同一 zone 再進入時只保留最新 SETUP，不會誤刪其他 OB/FVG 的 SETUP；重疊 zone 應歸屬最近 midpoint。
- [ ] 驗證同 zone 中未 ARMED 的 SETUP 會被替換，但已 ARMED 並暗化封存的 SETUP 在後續 re-entry 後仍保留。
- [ ] 在 H4、M15 驗證每個 SETUP 最多一個 ARMED，以及 zone 失效、反向 Daily MSS、20 bars 逾期的取消流程。
- [ ] 驗證 ARMED swing length、ATR multiplier 與 label 上限的邊界值。
- [ ] 在 H4、M15 Replay 驗證 ARMED 後首次回踩 ENTRY，以及 zone/bias/protect close/new SETUP 取消流程；另測 expiry=0 與正值。
- [ ] 驗證隱藏 SETUP 或 ARMED 標籤時，ENTRY 狀態流程仍維持一致。
- [ ] Replay 驗證 Trade Plan 從 ENTRY 下一根 K 才判定、SL/TP 同 K 採 SL 優先、TP1 後原 SL 保留，以及最多 20 組裁切。
- [ ] 驗證 Bullish/Bearish 非法 SL、最小跳動距離與 TP2 小於 TP1 的防護。
- [ ] 在 V1 開啟／關閉 `Show SL/TP trade plans`，確認統計數值完全相同且關閉時不建立 lines/labels。
