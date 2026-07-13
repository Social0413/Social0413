# TradingView SMC Replay Toolkit

> 專案知識已改為 Repository 內的規格先行文件。功能定義以 [SMC_SPEC.md](SMC_SPEC.md) 為入口；尚未驗證的項目不視為已完成。

## 專案知識索引

- [SMC_SPEC.md](SMC_SPEC.md)：現行 SMC 訊號、失效與顯示規格。
- [DESIGN.md](DESIGN.md)：資料流、狀態與 Pine 繪圖設計。
- [ROADMAP.md](ROADMAP.md)：後續驗證與候選方向。
- [CODING_RULE.md](CODING_RULE.md)：Pine 與版本控制規則。
- [CHANGELOG.md](CHANGELOG.md)：依 Git history 整理的變更摘要。
- [TEST_RESULT.md](TEST_RESULT.md)：已完成與待執行測試。
- [KNOWN_BUGS.md](KNOWN_BUGS.md)：已知限制與已修正問題。
- [TODO.md](TODO.md)：可執行待辦清單。
- [PROJECT_HISTORY.md](PROJECT_HISTORY.md)：完整開發歷程。

這個專案整理了一組給 Codex 使用的 TradingView 自動化技能，以及一個用於 SMC 回放分析的 Pine Script 指標。

專案目標是讓 Codex 可以協助開啟 TradingView、切到指定標的與週期，並用穩定、可版本控管的方式迭代 SMC 分析工具。

## 統計版本

### 研究環境與版本分工

- 使用者目前使用 TradingView **Essential** 方案；後續資料覆蓋、Pine request、圖表歷史與效能評估均以 Essential 限制為預設，不應以 Premium、Expert 或 Ultimate 能力推定可行。
- 目前研究市場為**台股**；長期資料量應按台股交易日與交易時段估算，不以 24/7 加密貨幣 bars 數量估算。
- V3 定位為多週期批次統計引擎：一次取得 M30、H1、H4 統計；程式已固定關閉交易細節與 zone/structure 繪圖，只保留統計表，以降低長 Window 的物件、記憶體與執行時間壓力。此修改仍待 Pine Editor compile 與實圖驗收。
- V1 定位為單引擎細部檢查工具：當 V3 某個 symbol、timeframe 或結果值得研究時，再以 V1 檢查 SETUP、ARMED、ENTRY、SL/TP 與逐筆交易細節。
- V1、V3 與 V4 的統計 Window 現行規格均固定為 3 年（1095D），設定介面不再提供其他年數。V3 可在 M30/H1/H4 chart 執行；V4 為確保三套 Top-down models 的主資料軸與 lower-timeframe arrays，固定以 H4 chart 作為執行入口。
- 近期策略開發主流程聚焦最近 3 年：選定多檔流動性與交易特性相近的台股，全部使用同一套規則與參數。V3 比較三引擎及跨股票一致性；V1 抽樣檢查好、中、差交易的 SETUP → ARMED → ENTRY → SL/TP 是否合理。規則修改後必須重跑完整股票池，不依單一股票建立專用參數。
- 舊版 V3 曾做過 5 年／7 年 coverage 研究；現行版本已依最新決定移除這些選項，不再作為可選 Window。

- `smc-weekly-ob-fvg/assets/smc_weekly_ob_fvg_v1.pine`：目前圖表週期即為 Entry timeframe；在 H4、H1、M30 顯示該週期的詳細交易統計與訊號漏斗。
- `smc-weekly-ob-fvg/assets/smc_weekly_ob_fvg_compare_v2.pine`：固定在 M30 圖表執行，並在同一張表比較 H4、H1、M30 的 SETUP、ARMED、交易數、TP1/TP2 命中率、Net R、Avg R 與 Profit Factor。
- V2 目前不是跨圖表週期固定計算版本；切換到非 M30 圖表時只顯示 `USE M30 CHART`。
- `smc-weekly-ob-fvg/assets/smc_weekly_ob_fvg_cross_tf_v3.pine`：獨立的 Cross-Timeframe Stats 版本；以 M30 為基礎資料流，在 M30/H1/H4 圖表逐筆回放完成 M30 bars，右下角固定顯示 H4、H1、M30 三列統計與資料覆蓋狀態。
- `smc-weekly-ob-fvg/assets/smc_top_down_models_v4.pine`：獨立的 Top-down Model Research 版，不覆蓋 V3；在 H4 chart 比較 `W→D→H4`、`D→H4→H1`、`H4→H1→M30` 三套完整模型。Weekly／Daily／H4 context 均使用多 Zone arrays，並分開顯示 SETUP、Unique SETUP 與 replacement。V4 已完成 Pine Editor compile；其中 `SWING W-D-H4` 已用 2105、1504、2324 驗證所有 V1 可比統計完全一致，可搭配 V1 完整繪圖進行策略微調。
- `smc-weekly-ob-fvg/assets/smc_three_engines_architecture.svg`：三套 Top-down models 的架構與 timeframe 關係圖。
- V3 已在 TradingView 完成 Pine Editor compile，並以相同 symbol、365D Window 比對 M30/H1/H4 圖表；三列 SETUP、ARMED、Trades 與績效欄位一致。表格出現 `3TF PARTIAL` 時仍不可視為完整 Window 結果。

## 目前功能

### open-tv

開啟本機 TradingView Desktop。

- 使用 `tradingview://` 啟動 TradingView。
- 不做下單、券商連線、帳戶設定或圖表版面修改。

### close-tv

關閉本機 TradingView Desktop。

- 搜尋 TradingView 相關程序。
- 只關閉 TradingView，不碰其他程式。

### open-tv-symbol

開啟指定標的與週期。

- 支援像 `ETH H4` 這類輸入。
- 預設加密貨幣會轉成 `BINANCE:<BASE>USDT`。
- `H4` 會轉成 TradingView interval `240`。
- 若 `tradingview://chart/...` 不穩，改用 TradingView 網頁 URL：

```powershell
Start-Process 'https://www.tradingview.com/chart/?symbol=BINANCE%3AETHUSDT&interval=240'
```

### smc-weekly-ob-fvg

TradingView Pine Script 指標，用於 SMC 高時框分析。

目前指標檔：

```text
smc-weekly-ob-fvg/assets/smc_weekly_ob_fvg_v1.pine
```

目前規則：

- 使用週線作為高時框來源。
- 繪製週線 OB 與 FVG。
- 看多 OB 用綠色。
- 看空 OB 用紅色。
- FVG 使用黃色系：做多 FVG 較亮，做空 FVG 較暗。
- FVG 使用標準三根完成 K 的 wick-to-wick gap；不設最小 gap 寬度，中間 K 必須與 FVG 同方向且實體至少為來源時框 Wilder ATR(14) × 1.0。
- OB 使用結構突破規則：
  - 週收盤突破近期結構高且突破實體至少為完成週線 Wilder ATR(14) × 1.0，回找該 displacement 前最近 bearish 週 K 當 bullish OB。
  - 週收盤跌破近期結構低且突破實體至少為完成週線 Wilder ATR(14) × 1.0，回找該 displacement 前最近 bullish 週 K 當 bearish OB。
- OB 固定使用 Hybrid Range：Bullish 為來源 K 的 `low → open`，Bearish 為來源 K 的 `open → high`。
- 同一方向、同一根週 K 只能生成一次 OB。
- OB/FVG 過中線後停止延伸。
- OB/FVG 文字永遠顯示。
- 額外用水平線標記日線級別的 CHOCH 與 MSS；在日線圖直接判斷，在其他時框只顯示日線訊號，不用目前時框重新判斷。
- 專案時框分工固定為：週線判斷 OB/FVG，日線判斷 CHOCH/MSS。
- 做多 CHOCH/MSS 使用綠色系，做空 CHOCH/MSS 使用紅色系；CHOCH 較暗，MSS 較亮。
- CHOCH/MSS 線從下一根 K 開始，若 K 棒碰到該線位就停止延伸。
- CHOCH 只保留結構線、不顯示文字；MSS 保留線段中間的 `MSS` 文字，不另外建立獨立標籤。
- SETUP：最新 Daily MSS 方向與目前 K 棒觸及的有效 Weekly OB/FVG 同向時，每次進區顯示一次 `B SETUP` 或 `S SETUP`；每個 zone 只替換尚未 ARMED 的舊 SETUP，已進入 ARMED 的暗色 SETUP 會保留。
- 第二步 ARMED：SETUP 後，當目前圖表時框收盤突破 confirmed pivot 且 K 棒實體通過 ATR displacement 時，顯示一次 `B ARMED` 或 `S ARMED`；不畫進場線。
- ARMED 成立後，對應 SETUP 會自動暗化，讓視覺焦點保留在最新流程階段。
- 第三步 ENTRY：ARMED 後等待首次回踩突破位並收盤重新確認，顯示一次 `B ENTRY` 或 `S ENTRY`；仍不畫 SL/TP，也不會送單。
- ENTRY 等待目前預設不限期；保護 swing 必須被收盤突破才取消，不因單純影線觸及而取消。
- 第四步 Trade Plan：ENTRY 收盤建立 Entry/SL/TP1/TP2 計畫，預設 TP1=1R、TP2=2R，從下一根 K 開始以 SL 優先規則追蹤 `TP1 HIT`、`WIN TP2` 或 `LOSS`；最多保留 20 筆，不會實際送單。
- 已移除 365 天高低點，降低回放模式與長歷史掃描的資源壓力。
- `Maximum zones per type` 目前預設為 `40`，降低 TradingView 回放模式的物件壓力。

## 回放模式狀態

目前已確認：

- 月時框回放較穩。
- 日線與週線回放曾出現整個指標消失的情況。
- 問題不是單一 OB/FVG 中線失效，而是 TradingView Pine 在 replay 中重算 box/line 物件時的穩定性與物件壓力問題。

目前已採取的穩定化措施：

- 降低最大區塊數。
- 移除 365D 高低點掃描。
- 避免同一 OB 來源 K 棒重複生成。
- 移除 Debug label，減少額外 label 物件。

## 版本控管

GitHub repo：

```text
https://github.com/Social0413/Social0413
```

之後每次修改完成後，直接 commit 並 push 到 GitHub，避免迭代中失去可回復版本。

## 專案命名

這段開發歷程命名為：

```text
TradingView SMC Replay Toolkit
```

它代表從 TradingView 啟動技能、指定標的週期開圖，到 SMC 週線 OB/FVG 回放分析工具的完整開發過程。
