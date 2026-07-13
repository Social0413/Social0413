# SMC 功能規格

## V4 Top-down Model Research Engine（開發版）

- V4 必須是獨立 Pine 檔，不覆蓋 V3；V3 保留作為同一 Weekly context 下的 H4/H1/M30 比較基準。
- V4 固定使用 `1095D`（3 年）統計 Window，建議載入於 H4 chart，以 H4 主時間軸搭配 lower-timeframe arrays 重建 H1/M30。
- V4 比較三套完整模型，而不是只替換 Entry timeframe：
  - `SWING`：Weekly OB/FVG zone → Daily MSS bias → H4 execution。
  - `INTRADAY`：Daily OB/FVG zone → H4 MSS bias → H1 execution。
  - `FAST`：H4 OB/FVG zone → H1 MSS bias → M30 execution。
- 每套模型必須擁有獨立的 zone、bias、SETUP、ARMED、ENTRY 與 trade state，不得共用 active SETUP/ARMED。
- Weekly、Daily、H4 Zone Engine 均採用與 V1 相同的多 Zone arrays：OB/FVG 每類最多 40 個、OB source 去重、OB 結構突破 candle 必須通過來源時框 ATR(14) × 1.0 displacement、OB 固定使用 Hybrid Range；FVG 使用標準三根完成 K 的 wick-to-wick gap，中間 candle 必須為同方向 ATR(14) × 1.0 displacement。所有 active zones 共同參與最近 midpoint touch 選擇，並由 execution bar close 穿越 midpoint 失效。
- SWING 的 Daily MSS bias 使用與 V1 H4 chart 相同的完成 Daily candle 聚合、confirmed pivot、最近 `ATR length` 根 True Range 平均與 trend-reversal MSS 更新規則，作為 V1↔V4 對答案基準。
- 相同 symbol、H4 chart、1095D、參數與資料覆蓋下，驗收對應為：V1 `SETUP` = V4 SWING `SETUP`、V1 `ARMED` = V4 SWING `ARMED`、V1 `Total` = V4 SWING `Total`，且 TP2 Rate、Net R、Profit Factor、OB/FVG 與 replacement 分類都必須一致；任何差異都視為待追查，不以模型差異解釋。
- V4 使用與 V1 相同的統計名稱：SETUP replaced、ARMED replaced、OB SETUP、FVG SETUP、Same-zone SETUP、Changed-zone SETUP。`UNIQUE SETUP`、`U>A`、`A>T` 是 V4 額外研究欄位，沒有 V1 對應欄位。
- 完成的高週期 context 才能供低週期模型使用；不得將同一高週期 candle 的最終值回填至其內部較早 intrabars。
- Funnel 分開顯示 `Raw SETUP`、`Unique SETUP`、`Replaced`、`ARMED`、`TRADES`；轉換率使用 `Unique SETUP` 作為分母。
- `Unique SETUP` 定義為沒有 active SETUP 時建立的新 SETUP episode；active SETUP 期間的新 zone touch 只計入 `Raw SETUP` 與 `Replaced`。若新 SETUP 取代 active ARMED，該 SETUP 是新的 episode，並同時計入 replacement。
- V4 已完成 Pine Editor compile 與 H4／1095D 實圖驗證；2105、1504、2324 的 `SWING W-D-H4` 所有 V1 可比欄位完全一致。INTRADAY 與 FAST 尚未與各自的完整繪圖基準逐筆核對。

本文件是 `smc-weekly-ob-fvg/assets/smc_weekly_ob_fvg_v1.pine` 的現行行為基準。若文件與程式不一致，以待確認的需求為準，不應直接把差異視為新規格。

## Timeframe

- OB 與 FVG 來源為完成的 Weekly candle；程式預設 `High timeframe = W`。
- Daily chart 直接使用日線 candle 計算 CHOCH/MSS。
- Intraday chart 先由圖表 bars 聚合完成的 Daily candle，再套用同一組 Daily 結構邏輯；不得以 H4/M15 結構取代 Daily 結構。

## Order Block (OB)

- Bullish OB：完成的 Weekly close 突破最近 `structureLookback` 根完成週線的結構高點，且突破 candle body 至少為完成週線 Wilder ATR(14) × 1.0；之後向前尋找該 displacement 前最近的 bearish weekly candle。
- Bearish OB：完成的 Weekly close 跌破最近 `structureLookback` 根完成週線的結構低點，且突破 candle body 至少為完成週線 Wilder ATR(14) × 1.0；之後向前尋找該 displacement 前最近的 bullish weekly candle。
- 搜尋範圍由 `OB candle searchback` 控制，預設 8。
- OB range 固定使用 Hybrid Range，不提供 Wick／Body 切換：Bullish OB 使用來源 bearish candle 的 `low → open`；Bearish OB 使用來源 bullish candle 的 `open → high`。
- 同一來源 Weekly candle、同一方向最多建立一次 OB。
- Bullish OB 為綠色；Bearish OB 為紅色；box 內顯示 `OB`。
- Bullish OB 在收盤價低於 midpoint 時停止向右延伸；Bearish OB 在收盤價高於 midpoint 時停止延伸。

## Fair Value Gap (FVG)

- 使用三根完成 Weekly candle：Bullish FVG 為第三根 low 高於第一根 high；Bearish FVG 為第三根 high 低於第一根 low。
- Gap 只要求符合標準 wick-to-wick 幾何條件，不使用價格百分比或 ATR 最小寬度門檻。
- 中間 Weekly candle 必須與 FVG 同方向，且 candle body 至少為完成週線 Wilder ATR(14) × 1.0：Bullish FVG 要求中間 candle 收紅／上漲，Bearish FVG 要求中間 candle 收黑／下跌。
- FVG 從確認該 gap 的 Weekly candle 開始繪製。
- Bullish FVG 使用較亮黃色；Bearish FVG 使用 olive/darker yellow；box 內顯示 `FVG`。
- Bullish FVG 在收盤價低於 midpoint 時停止延伸；Bearish FVG 在收盤價高於 midpoint 時停止延伸。

## CHOCH

- 使用獨立 pivot 系統，預設 swing length 2。
- Daily close 突破最新 pivot，且方向與已追蹤 trend 相反時產生 `CHOCH`。
- 線段由被突破的 Daily pivot candle 畫到確認突破的 Daily candle，不向未來延伸。
- CHOCH 只顯示線段，不顯示 `CHOCH` 文字，以降低圖表雜訊。
- Bullish 使用深綠、Bearish 使用深紅。

## MSS

- 使用獨立 pivot 系統，預設 swing length 5。
- 除結構突破與 trend 反轉外，突破 candle body 必須符合 ATR displacement filter。
- ATR length 預設 14，body multiplier 預設 1.0；設為 0 可停用 displacement 門檻。
- 線段範圍同 CHOCH，但 MSS 保留 `MSS` 文字；Bullish 使用亮綠、Bearish 使用亮紅。

## 顯示與資源限制

- OB/FVG 每類預設最多 40 個；CHOCH/MSS 線預設最多 120 個，超限時刪除最舊物件。
- `Stable replay extension` 預設開啟，使用 `extend.right`；midpoint invalidation 仍必須生效。
- 已移除 365D High/Low，以降低 Replay object pressure。
- Pine indicator 宣告上限為 500 boxes、500 lines、200 labels；這是平台物件上限設定，不是保證所有歷史區間皆可無限制顯示。

## SETUP（進場開發第一階段）

- SETUP 僅為圖上提示，不送出訂單，也不連接交易所或券商。
- 最新完成的 Daily bullish/bearish MSS 決定目前 bias，直到反方向 Daily MSS 出現。
- 目前圖表 K 棒的 high/low 與仍有效、同方向的 Weekly OB 或 FVG 重疊時，視為進入 zone。
- Bullish bias 與 bullish zone 同時成立時顯示綠色 `B SETUP`；Bearish bias 與 bearish zone 同時成立時顯示紅色 `S SETUP`。
- 同一次連續停留在 zone 內只顯示一次；離開後再次進入可重新顯示。
- 每個確切 Weekly zone 只保留最新一個尚未 ARMED 的 SETUP；重新進入同一 zone 時刪除該 zone 舊的等待中標籤並建立新標籤。不同 zone 的 SETUP 互不刪除。
- OB/FVG 重疊時，SETUP 歸屬於 midpoint 距目前收盤價最近的有效 zone。
- 價格持續與多個重疊 zone 接觸時，若依上述規則選出的 zone key 改變，視為進入另一個 zone，可產生新的 SETUP。
- SETUP 標籤最多保留最新 40 個，可由 `Maximum SETUP labels` 向下調整；超限時刪除最舊標籤，不影響訊號判定。

## ARMED（進場開發第二階段）

- 每個 SETUP 建立一個等待中的 ARMED 候選；新的 SETUP 會取代前一個尚未完成的候選。
- 重新進入同一 zone 時，已完成的歷史 ARMED 標籤保留；只有尚未完成的 ARMED 候選被新 SETUP 取代。
- 使用目前圖表時框的 confirmed pivot，預設 swing length 3；Bullish SETUP 等待收盤向上突破 swing high，Bearish SETUP 等待收盤向下突破 swing low。
- 突破必須由前一根收盤尚未越過、目前收盤正式越過，且 candle body 預設至少為目前圖表時框 ATR(14) 的 1.0 倍。
- 成立時顯示 `B ARMED` 或 `S ARMED`，同一 SETUP 最多一次，不畫水平線。
- ARMED 成立時，將同一 zone 對應的最新 SETUP 標籤改為較暗、較透明並封存；封存後不再被同 zone 的後續 SETUP 取代，確保 SETUP → ARMED → ENTRY 歷史鏈仍可辨識。
- SETUP 所屬 Weekly zone 失效、出現反向 Daily MSS，或等待超過預設 20 根圖表 K 時取消候選。
- ARMED 標籤最多保留最新 40 個，可由 `Maximum ARMED labels` 向下調整。

## ENTRY（進場開發第三階段）

- ARMED 成立時保存被突破的 pivot level、來源 zone、ARMED bar，以及反方向最近 confirmed pivot 作為保護 swing。
- ENTRY 必須發生在 ARMED 之後的 K 棒；Bullish 為 low 回到或跌破突破位且收盤重新站上，Bearish 為 high 回到或突破突破位且收盤重新跌回其下。
- 每個 ARMED 最多產生一個 `B ENTRY` 或 `S ENTRY`，只顯示小標籤，不畫水平線。
- 原 zone 失效、Daily MSS bias 反向、收盤突破反方向保護 swing，或出現新 SETUP 時取消 ENTRY 候選。
- `ENTRY retest expiry bars` 預設為 0，代表不限期；設為正整數時，等待超過指定圖表 K 棒數才取消。
- ENTRY 標籤最多保留最新 40 個，可向下調整；SETUP、ARMED、ENTRY 三類合計設定上限為 120，低於 indicator 的 200 labels 宣告上限。
- `Show SETUP`、`Show ARMED`、`Show ENTRY` 僅控制各階段標籤顯示，不改變狀態判定與後續流程。
- ENTRY 標籤本身不代表實際送單；Stop/TP 的圖表追蹤由下方 Trade Plan 階段處理。

## 交易統計與週期比較

- V1 不提供 `Entry timeframe` 選項；目前圖表週期即為 Entry timeframe。H4、H1、M30 圖表分別直接計算該週期 SETUP/ARMED/ENTRY，其他圖表不建立新候選並顯示切換提示。
- 統計期間由 `Statistics lookback days` 控制，可選 90、180、365、730 天；期間開始前不建立候選交易。
- `SETUP expiry hours` 預設 24 小時，依 Entry timeframe 換算 bars；H4/H1/M30 分別為 6/24/48 bars。
- 預設 TP1 平倉比例為 50%；預設 TP1=1R、TP2=2R 時，WIN TP2=+1.5R、TP1→LOSS=0R、Direct Loss=-1R。
- 同一根 K 同時觸及 SL/TP 時維持 SL 優先。
- V2 只在 M30 圖表計算；內部分別以 M30、完成 H1 K、完成 H4 K 維護三套獨立 SETUP/ARMED/ENTRY 與交易結果，表格僅做比較顯示。
- V3 Cross-Timeframe Stats 以完成的 M30 bars 作為唯一基礎資料流；M30 圖表直接逐 bar 計算，H1/H4 圖表使用 `request.security_lower_tf()` 取得每根圖表 K 棒內依時間排序的 M30 intrabars 並逐筆回放。
- V3 必須由 M30 基礎資料流分別驅動 M30、完成 H1 K、完成 H4 K 三套獨立 SETUP/ARMED/ENTRY 與交易狀態，表格固定顯示 M30、H1、H4 三列。圖表週期不得改變任何一列結果；非 M30/H1/H4 圖表顯示切換提示，不宣告支援。
- V3 只納入已完成的 M30 bars；即時尚未完成的 M30 bar 不得提前計入。若 TradingView intrabar 歷史覆蓋不足，表格必須顯示資料覆蓋警告，不能把部分歷史結果標示為完整同步。
- V1、V3 與 V4 現行統計 Window 均強制固定為 1095D；V1/V3 不再顯示 Window 選項。V3 可在 M30/H1/H4 執行，V4 固定使用 H4 chart。
- V3 為純統計版本，不建立 Weekly zone、CHOCH/MSS、SETUP/ARMED/ENTRY 或 Trade Plan 的 box、line、label；上述規則仍以純數值狀態驅動 M30/H1/H4 三套統計。逐筆視覺檢查使用 V1。
- V3 必須分別檢查 M30、H1、H4 的第一筆可用資料是否涵蓋 Window 起點；三列全部通過才顯示 `3TF V3 FULL`，否則顯示 `3TF PARTIAL` 並在列名標記 `PART`。

## Trade Plan（進場開發第四階段）

- ENTRY 成立時以確認 K 收盤作為 Entry，ARMED 保存的反方向 confirmed pivot 作為 SL；Bullish SL 必須低於 Entry，Bearish SL 必須高於 Entry，且距離至少一個 `syminfo.mintick`。
- Risk 定義為 `abs(Entry - SL)`；TP1 預設 1R、TP2 預設 2R。若使用者將 TP2 倍數設得低於 TP1，實際 TP2 自動採用 TP1 倍數作為下限。
- 每筆計畫建立 SL、TP1、TP2 三條短線與一個資訊標籤；線段由 ENTRY bar 開始，逐 bar 延伸到計畫結束。
- SL/TP 從 ENTRY 下一根 K 才開始判定，避免使用 ENTRY 確認 K 已發生的 high/low。
- 同一根 K 同時觸及 SL 與任一 TP 時，採保守的 SL 優先；TP2 觸及標示 `WIN TP2`，SL 觸及標示 `LOSS`，若先前已達 TP1 則標示 `TP1 → LOSS`。
- TP1 達成只更新為 `TP1 HIT`，不移動 SL，繼續等待原始 SL 或 TP2。
- 新 SETUP/ARMED/ENTRY 與原 Weekly zone 後續失效均不取消已建立的 Trade Plan；每筆計畫獨立追蹤。
- 最多保留最新 20 筆 Trade Plan，超限時整組刪除最舊的三條線與資訊標籤。
- Trade Plan 只供圖表分析，不使用 `strategy.entry()`，也不會實際送單。
- V1 的 `Show SL/TP trade plans` 只控制 SL/TP lines 與 plan labels；有效 ENTRY、交易狀態與績效統計永遠計算，不受顯示開關影響。
