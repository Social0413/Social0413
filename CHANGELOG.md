# Changelog

## 2026-07-16 V4-LONG-01 Taiwan equity Long-only execution sync

- V1 `V1-LONG-01` 通過 2105、2324/H1 實圖驗證後，相同 bullish SETUP touch gate 同步至 V4 PRIMARY。
- Bearish zones 與 bearish Daily structure 繼續維護，但不再建立 flow 或進入 V4 SETUP、ARMED、Total、來源分類及績效統計。
- midpoint invalidation、ENTRY/TPSL、one-trade-per-zone、compact table 與其餘計算均未修改。
- Repository 靜態檢查與 `git diff --check` 通過；V1/V4 bullish gate、唯一 flow push、Bearish context、midpoint 與 compact table 已完成交叉核對。
- 使用者已在 2105、2324/H1 驗證 V1/V4 所有共通欄位一致，Long-only 同步通過。

## 2026-07-16 V1-LONG-01 Taiwan equity Long-only execution

- 只修改 V1；V4 維持 `V4-STATS-01`，待 V1 驗證後才同步。
- SETUP touch gate 固定要求 bullish zone 與 bullish Daily Bias；Bearish zone 不再建立 execution flow，因此不再產生 S SETUP、S ARMED、S ENTRY、S Trade Plan 或空方統計。
- Bearish OB/FVG、Daily bearish MSS/CHOCH、Bias 轉空及取消既有多方候選的風險 context 保留。
- midpoint invalidation、Bullish SETUP／ARMED／ENTRY、ENTRY expiry、TP1→BE、one-trade-per-zone、compact table 與所有績效公式均未修改。
- Repository 靜態檢查與 `git diff --check` 通過；唯一 flow 入口、Bearish context 保留、midpoint 不變及 V4 未同步均已確認。
- 使用者在 2105、2324/H1 確認只出現 B SETUP／ARMED／ENTRY／PLAN，Bearish zone 仍顯示且無 S flow；同意同步 V4。

## 2026-07-16 V1-BEARVIS-01 bearish zone light-red palette

- 只修改 V1 Weekly zone 視覺；V4 無 zone 繪圖，不需同步。
- Bearish OB fill、border 與 `OB` 文字改為淺紅色；Bearish FVG fill、border 與 `FVG` 文字改為更淡的淺紅色，不再使用 olive。
- Bullish OB/FVG 顏色、midline、zone direction、建立、失效、SETUP／ARMED／ENTRY、Trade Plan 與全部統計均未修改。
- Repository 靜態檢查與 `git diff --check` 通過；兩階淺紅、olive 移除、Bullish 配色保留及 V4 未修改均已確認。
- 使用者在 2376/Weekly 確認 Bearish OB/FVG 淺紅配色沒有問題；視覺階段通過。

## 2026-07-16 V4-STATS-01 mobile compact statistics sync

- V1 `V1-STATS-01` 通過使用者視覺確認後，相同 `Show SETUP/ARMED/ENTRY statistics` 開關同步至 V4。
- 預設開啟時保留原 17 欄完整表格；關閉時表頭顯示 `COMPACT`，只重畫 MODEL、Total、TP2 Rate、Net R、Profit Factor 五欄。
- `FULL/PART` coverage 併入 MODEL 顯示；LEGACY OFF rows 在 compact 模式隱藏。
- 只修改 table 顯示，不改 V4 PRIMARY 的 Zone、Bias、SETUP、ARMED、ENTRY、Trade、績效 arrays 或任何計算。
- Repository 靜態檢查與 `git diff --check` 通過；預設值、full/compact headers、五欄資料來源、coverage 與 table clear 均已確認。
- 使用者在 2105/H1 確認 V4 COMPACT 正常顯示；V1/V4 同為 Total 1、TP2 Rate 0%、Net R -1R、Profit Factor 0，顯示同步通過。

## 2026-07-16 V1-STATS-01 mobile compact statistics

- 只修改 V1；V4 維持 `V4-ENTRYTPSL-01`。
- 新增預設開啟的 `Show SETUP/ARMED/ENTRY statistics` 顯示開關。
- 關閉時清除結果表的 `SIGNAL FUNNEL` 第 11～29 列，表頭顯示 `COMPACT`；保留 Total、Open、Win TP2、TP1→BE、Direct Loss、TP1/TP2 Rate、Net R、Avg R 與 Profit Factor。
- 開關只影響 table cells，不影響 SETUP／ARMED／ENTRY 圖上標籤、訊號判定、Trade Plan、統計累計或 V1/V4 策略核心。
- Repository 靜態檢查與 `git diff --check` 通過；預設值、table clear、row scope 與底層計數持續運作均已確認。
- 使用者已確認 `COMPACT` 模式內容符合手機顯示需求，並同意同步 V4。

## 2026-07-16 V4-ENTRYTPSL-01 ENTRY expiry and TP1 breakeven sync（部分通過）

- V1 `V1-ENTRYTPSL-01` 通過 2376、2324、2609/H1 TradingView 檢查後，相同核心同步至 V4 PRIMARY。
- `ENTRY retest expiry bars` 預設改為 15 根 H1；TP1 達成後將剩餘部位 stop 更新為 Entry。
- TP1 後觸及 Entry 的結果改為 `TP1 → BE`，R 值為已實現 TP1 部位的收益；預設 50% 於 1R 出場時為 +0.5R。
- `SL → TP2 → TP1` 的同 K 保守判定順序維持不變；Weekly zone、Daily MSS、SETUP、ARMED、ENTRY 幾何與 one-trade-per-zone 未修改。
- Repository 靜態檢查與 `git diff --check` 通過；V1/V4 的 expiry、stop→Entry、TP1→BE R 與 SL 優先順序已完成程式交叉核對。
- TradingView compile／runtime 通過；2609、2324/H1 的 V1/V4 共通統計完全一致，2376 尚待回歸。

## 2026-07-16 V1-ENTRYTPSL-01 ENTRY expiry and TP1 breakeven

- 只修改 V1；V4 維持已驗證的 `V4-MSS-02`。
- `ENTRY retest expiry bars` 預設由 0 改為 15 根 H1；到期只取消尚未 ENTRY 的候選，不消耗來源 zone。
- TP1 達成後，剩餘部位 SL 移到 Entry；後續觸及 Entry 記為 `TP1 → BE`。預設 50% 於 1R 出場時，此結果為 +0.5R。
- 首次觸及 TP1 的同一根 K 若同時觸及原始 SL，仍維持保守的 SL 優先並記為 Direct Loss。
- Weekly zone、Daily MSS Bias、SETUP、ARMED、ENTRY retest geometry、one-trade-per-zone、TP1/TP2 價格與其他失效條件均未修改。
- Repository 靜態檢查與 `git diff --check` 通過；已確認 stop array、SL line、TP1→BE R 計算及 `SL → TP2 → TP1` 判定順序一致。
- 使用者已在 2376、2324、2609/H1 確認正常執行；2324 與 2609 均出現一筆 TP1→BE，預設 +0.5R 計算正確，因此同意同步 V4。

## 2026-07-15 V4-MSS-02 close-break Daily MSS sync

- V1 `V1-MSS-02` 已由使用者在 2376/D 確認 MSS 圖形結果，相同核心同步至 V4 PRIMARY。
- 移除 V4 Daily MSS 的 ATR length、body multiplier、True Range arrays、平均計算及未使用的舊 bias helper；MSS 僅由較長 confirmed pivot、完成 Daily close 與 trend reversal 決定。
- 完成 Daily candle 改為先判斷先前 confirmed pivot，再發布本 candle 新確認的 pivot，與 V1 執行順序一致。
- Weekly zone ATR、SETUP／ARMED／ENTRY、15 根 H1 expiry、one-trade-per-zone、TPSL 與績效公式未修改。
- 使用者已在 2376/H1／1095D／FULL 驗證 V1/V4 共通欄位完全一致：SETUP 16、replaced 4、ARMED 3、Total 3、Net R -0.5R、OB/FVG 6/10、Same/Changed 6/10；同步完成。

## 2026-07-15 V1-MSS-02 close-break Daily MSS

- 只修改 V1；V4 維持已驗證的 `V4-ENTRY-01`。
- Daily MSS 移除 ATR length、body multiplier 與單根 displacement 條件；較長 confirmed pivot、完成 Daily close 正式突破與 trend reversal 直接成立 MSS。
- H1 聚合 Daily 同步移除專供 MSS ATR 使用的 open、close、True Range arrays 與平均計算，保留與 D chart 相同的純結構規則。
- 保留 `V1-MSS-01` 的先判斷舊 confirmed pivot、再發布本 candle 新 pivot 順序；Weekly OB/FVG 的 ATR 規則完全不變。
- 使用者已在 2376/D 確認正式版 MSS 圖形結果；H1 Bias／交易回歸於 V4 同步後共同核對。

## 2026-07-15 V1-MSS-01 prior-pivot breakout ordering

- 只修改 V1；V4 維持已驗證的 `V4-ENTRY-01`，待 V1 TradingView 驗證後才同步。
- D chart 與 H1 聚合 Daily 兩條路徑改為先用進入完成 candle 前已確認的 CHOCH/MSS pivot 判斷 breakout、事件與 Bias，再寫入本 candle 新確認的 pivot。
- 修正同一根完成 Daily candle 先把 pivot 更新到較新位置、再判斷突破而可能漏掉 MSS／CHOCH 的執行順序問題。
- swing length、ATR displacement、MSS reversal、Bias invalidation、Weekly zone 與 SETUP／ARMED／ENTRY／Trade Plan 規則均未修改。
- TradingView compile 通過，但 2376/D 缺少的 bearish MSS 仍未出現；將 MSS multiplier 暫設 0 後即出現，證明本案例的直接原因是 ATR displacement。ordering 修正保留作為正確事件邊界，MSS ATR 規則另於 `V1-MSS-02` 移除。

## 2026-07-15 V4-ENTRY-01 one trade per exact zone sync

- V1 `V1-ENTRY-01` 已由使用者在 2376/H1 驗證完整交易鏈保留及同 zone 後續 SETUP 排除；V1 SETUP 14、ARMED 3、Total 3、Net R -0.5R。
- 相同 `traded` zone state 已同步至 V4 PRIMARY；有效 Trade 建立後，同一 exact zone 不再建立第二個 SETUP episode。
- V4 zone 建立、按類型裁切、touch 與 Trade 成立路徑均同步維護 `traded` 平行 array；LEGACY 模型仍維持關閉。
- 使用者已在 2376/H1 驗證 V1/V4 共通欄位完全一致：SETUP 21、ARMED 4、Total 3、Net R -0.5R、OB/FVG 11/10、Same/Changed 12/9；V4 同步通過。

## 2026-07-15 V1-ENTRY-01 one trade per exact zone

- 只修改 V1；V4 維持已驗證的 `V4-AR-02R1`，待 TradingView 驗證後才同步。
- OB/FVG 每個確切 zone 新增獨立 `traded` 狀態；有效 ENTRY 成功建立 Trade Plan 時立即標記 consumed，同 zone 後續 touch 不再建立 SETUP。
- SETUP expiry、ARMED 失效與無效 Trade Plan 不消耗 zone；不同 key 的重疊 zone 仍可各自形成一筆交易。
- 有效交易的 SETUP／ARMED／ENTRY 標籤封存為完整歷史鏈，不再被同 zone 後續 SETUP 刪除。
- 使用者已在 2376/H1 驗證通過：完整交易鏈保留、同 zone 後續 SETUP 排除；SETUP 14、ARMED 3、Total 3、OB/FVG 2/12、Same/Changed 6/8、Net R -0.5R。

## 2026-07-15 V1-AR-02S1 latest SETUP label per zone

- 純 V1 顯示修改；V4 無 SETUP 視覺標籤，不改策略核心。
- 同一個確切 zone 建立新 SETUP 時，刪除該 zone 先前仍 active 或已封存的 SETUP 標籤，再建立最新標籤；不同 zone 仍可各顯示一個。
- SETUP、SETUP replaced、ARMED、Total、來源分類、active-flow lifecycle 與績效累計完全不變；尚待 TradingView 視覺與統計回歸。
- 2376、1504、2105、2324/H1 驗證通過；同 zone 標籤已收斂為最新一個，所有 V1/V4 共通統計維持一致。

## 2026-07-14 V1-AR-02 fixed pre-SETUP structure break

- 只修改 V1；V4 維持已驗證的 `V4-AR-01`。
- SETUP 建立時固定保存當下最後一個同方向 confirmed H1 pivot 作為 break level；等待期間的新 pivot 只更新全域最近 pivot 與後續 protect swing，不再移動既有 SETUP 的 break level。
- SETUP 後由 H1 close 正式穿越固定 break level 即成立 ARMED；移除 `ARMED ATR length`、`ARMED body ATR multiplier` 與 candle-body displacement 判定。
- 15 根 H1 expiry、zone／bias 失效、per-zone replacement、ARMED protect swing 與其他流程維持不變；尚待 TradingView compile、runtime 與訊號位置驗證。
- TradingView 1504、2105、2324/H1 驗證可正常執行；Net R 由舊版 `0R／-1R／-1R` 改為 `1.5R／0R／0R`，初步正面。仍需逐筆確認 ARMED 是否確實更早且不追高，本版暫不同步 V4。
- 使用者追加檢視 2376、2002 後接受此 ARMED 邏輯；相同核心已同步為 `V4-AR-02`，尚待 TradingView V1/V4 對齊驗證。
- `V4-AR-02` 初驗時 1504、2105 對齊，但 2324 的 V1 為 Total 0／0R、V4 為 Total 1／-1R；根因為 V4 在同 bar 先更新 pivot 再建立 SETUP，與 V1 順序相反。
- V4 改為先依先前 confirmed pivot 建立 SETUP，再寫入當根新 confirmed pivot，build ID 更新為 `V4-AR-02R1`；V1 未修改。
- `V4-AR-02R1` 已在 2324/H1 對齊 V1：SETUP 8、replaced 1、ARMED 1、Total 0、Net R 0R；待 1504、2105 回歸。
- 1504、2105 回歸亦完全對齊；`V1-AR-02`／`V4-AR-02R1` 已通過三標的 H1／1095D／FULL 所有共通欄位驗證，fixed pre-SETUP pivot ARMED 本輪完成。

## 2026-07-14 V1-AR-01 ARMED expiry and displacement

- ARMED 第一輪先只修改 V1；V1 通過三標的 TradingView 驗證後，才同步 V4。
- 將 SETUP → ARMED 等待期限統一為 15 根 H1；程式原已使用 `SETUP expiry H1 bars = 15`，本次同步修正規格中殘留的 20 根描述，不新增第二套 ARMED expiry。
- 將 V1 `ARMED body ATR multiplier` 預設值由 1.0 降為 0.5；confirmed pivot、close crossover、break level 更新、protect swing、zone／bias 失效與 per-zone lifecycle 均維持不變。
- `V1-AR-01` 已在 TradingView 1504、2105、2324/H1／1095D／FULL 正常執行；SETUP、ARMED、Total 與 V4 舊基準一致，三檔 ARMED 數量未增加。
- 相同核心已同步為 `V4-AR-01`；TradingView 1504、2105、2324/H1／1095D／FULL 均正常執行，所有 V1/V4 共通欄位完全一致，ARMED A＋B 本輪驗證完成。

## 2026-07-14 V1-PZ-04 FULL default and SETUP history

- 使用者在 `1504/H1`、`2105/H1`、`2324/H1` 確認 `V1-PZ-03 / FULL` 可正常執行，未發生空白或 crash。
- 定位到 FULL 的 SETUP 計數正常，但舊版會在 expiry、Bias flip、Zone invalid 與 replacement 時刪除歷史 SETUP 標籤。
- `V1-PZ-04` 保留最近 40 個歷史 SETUP 標籤；flow lifecycle、funnel 計數與交易判定不變，並將 diagnostic 預設改為 `FULL`。
- 使用者已在 1504、2105、2324/H1 確認歷史 SETUP 顯示正常，且統計未因顯示修正改變。
- V4 升版為 `V4-PZ-04`，同步 V1 已驗證的兩段式負索引檢查並預設 `FULL`；V4 不新增 V1 的視覺標籤，等待三檔統計對齊驗證。
- 使用者已在 1504、2105、2324/H1 驗證 `V4-PZ-04 / FULL`，所有 V1/V4 共通欄位一致；SETUP 階段完成，下一步為 ARMED 精修。

## 2026-07-14 V1-PZ-03 TOUCH negative-index fix

- 使用者以 `2324/H1` 確認 V1 `PZ OFF` 正常、`PZ TOUCH` 全部消失，V4 `PZ OFF` 同時保持正常。
- 修正 V1 Pine v5 在 `fi = -1` 時仍因非 lazy `or` 評估而執行 `array.get(flowStages, fi)` 的負索引 runtime failure。
- 修正僅套用 V1；V4 未修改。使用者已在 `2324/H1` 與 `1504/H1` 驗證 `V1-PZ-03 / PZ TOUCH` 可正常顯示，SETUP 分別為 8 與 20；`FULL` 尚未驗證。

## 2026-07-14 V1-PZ-01 / V4-PZ-02 stable diagnostic baseline

- Added visible build IDs to both indicator names and result-table headers.
- Added V1 `OFF / TOUCH / FULL` per-zone diagnostic stages to isolate the H1 blank-output problem.
- Defaulted V1 diagnostic mode to `OFF`, so Weekly zones and the stats table can be verified before enabling the new SETUP engine.
- Follow-up `V4-PZ-02` adds an `OFF / FULL` engine switch, default `OFF`, after V4 was confirmed to disappear only on H1.
- Verified 1504、2105、2324 on H1 with V1/V4 loaded together in `PZ OFF`; Weekly zones and both tables render normally.
- Rolled back the unverified `V1-PZ-02 / V4-PZ-03` optimization after `FULL` still caused blank H1 output.
- Reorganized Repository MD ownership and added `CLOSEOUT_CHECKLIST.md`; current status, target specification, tests, bugs and next actions are now explicitly separated.

## Earlier retained development changes

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
