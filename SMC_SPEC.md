# SMC 功能規格

> 狀態說明：V1 `V1-LONG-01`／V4 `V4-LONG-01` 已在 2105、2324/H1 完成 Long-only TradingView 共通統計對齊。Bearish zone／Daily structure 繼續顯示，midpoint invalidation 不變。

## V10 新架構：Weekly Structure Bias + Daily OB/FVG

- V10 必須使用獨立 Pine 檔，不得覆蓋或改寫穩定版 V1／V4。
- V10 檔案為 `smc-weekly-ob-fvg/assets/smc_weekly_structure_bias_v10.pine`；目前候選 build 為 `V10-DZONE-09`。Canonical Weekly table、第一輪 Daily zones 與 DZONE-07 Daily BOS line 視覺均已通過；DZONE-08 的 extreme opposing OB source 與 DZONE-09 的 ETH 統一仍待 TradingView 完整驗證。
- Weekly 的新核心職責是提供方向，不以 Weekly OB/FVG 的生成、存在、重疊或 touch 限制交易區域。
- V10 不再包含 Weekly OB/FVG inputs、arrays、boxes、midlines、invalidation、touch、traded state 或任何依賴 Weekly zones 的 execution。
- Bias 固定使用完成的 Weekly candles 與獨立 confirmed pivot，第一版 swing length 預設 2。
- Weekly Bias Engine 固定在 `request.security(..., "W", confirmedWeeklyBiasSnapshot(), lookahead=barmerge.lookahead_on)` 的 Weekly context 計算，並以一根歷史位移只發布上一根已完成 Weekly snapshot。Daily、H4、H1 不再各自聚合 Weekly OHLC 或維護各自的 pivot／Bias／flip state。
- 相同 symbol 與 Replay 位置下，Daily、H4、H1 的 W BIAS、W SWING HIGH、W SWING LOW 與 W FLIPS B/S 必須完全一致；任一欄不同即視為 canonical Weekly 驗證失敗。
- 完成 Weekly close 高於先前已確認的 Weekly swing high 時，Bias 更新為 Bullish；完成 Weekly close 低於先前已確認的 Weekly swing low 時，Bias 更新為 Bearish；未突破時維持原方向。
- 每根完成 Weekly candle 必須先對先前 confirmed pivot 判斷 Bias，再發布由本 candle 新確認的 pivot，避免 breakout target 被同 candle 新 pivot 回溯移動。
- Weekly Bias 不使用 ATR、單根 body displacement、Weekly OB/FVG touch 或 zone invalidation。
- `V10-WBIAS-04` 顯示目前 `週多 BULLISH／週空 BEARISH／中性 NEUTRAL`、confirmed swing high／low、Bias flip 次數、可選結構水平線、方向切換標記及可關閉的淡色背景。
- `Show Weekly Bias swing levels` 預設關閉；背景預設開啟。顯示開關不得改變 Weekly Bias state。
- Weekly Bias 不使用獨立左側表格；目前與 BUILD、confirmed levels、flips、phase、Daily zone 狀態整合在右上永久表。
- `V10-DZONE-09` 在 canonical Weekly Structure Bias 上顯示 canonical Daily OB/FVG 與 OB BOS structure line，但仍不包含 SETUP／ARMED／ENTRY、Trade Plan 或績效統計。
- Daily 與 H1 必須使用完全相同的 OB/FVG event、source time、top、bottom 與失效日，這是後續 H1 execution 的必要前提；任一 zone 只在其中一個時框出現即視為失敗。
- V10 的唯一 session 基準為 ETH。Canonical Weekly／Daily feed 必須使用 `ticker.modify(syminfo.tickerid, session.extended)`；TradingView 的 Daily、H4、H1 Replay 與未來 execution 驗收也必須把原生 intraday 圖表切到 ETH。RTH 圖表可能排除日終／延伸時段成交，不能用來否定 canonical Daily BOS、OB 或 FVG。
- Pine 無法切換原生圖表的 RTH／ETH。Intraday 圖表不是 ETH 時，右上 SESSION 必須顯示 `USE ETH (...)`；該警告只表示圖表 K 棒與 canonical feed 的 session 不同，不改變 canonical zone 計算。
- Daily Zone Engine 固定在 `request.security(..., "D", confirmedDailyZoneSnapshot(), lookahead=barmerge.lookahead_on)` 的 Daily context 計算，並以一根歷史位移只發布上一根已完成 Daily snapshot。Daily ATR、confirmed pivot、BOS、來源 K 搜尋與 FVG geometry 不再由 chart bars 各自重建。
- Daily 與 intraday chart 都只在 canonical Daily time 改變時消費一次 snapshot：先以完成 Daily close 失效既有 zones，再建立該完成日新確認的 OB/FVG。Box/line objects 仍在 chart context 建立，不放進 `request.security()`。
- Daily OB 使用獨立 confirmed pivot，`Daily OB swing length` 預設 4。每根完成 Daily candle 必須先對進入該 candle 前已確認的 swing high／low 判斷 BOS，再發布由該 candle 新確認的 pivot。
- 每個 confirmed swing 只接受第一次完成 close 穿越作為 BOS；該次突破即消耗此 pivot，不因價格回到結構內後再次穿越而重複判斷。Bullish Daily OB 只在前一個完成 Daily close 尚未高於 confirmed swing high、目前完成 close 首次收在其上方、突破 K 為 bullish，且 body 至少為 Daily Wilder ATR(14) × 1.0 時成立；Bearish Daily OB 對稱使用 confirmed swing low、bearish candle 與相同 ATR 門檻。首次 BOS 若未通過方向／ATR displacement，不建立 OB，也不以同一 pivot 的後續 re-cross 補建。
- Daily OB source 的搜尋區間是開區間：嚴格位於被突破 confirmed pivot K 之後、形成 BOS 的突破 K 之前；左右端點都不納入，也不使用固定根數 searchback。
- Bullish BOS 只在該區間的 bearish Daily candles 中選擇 `low` 最低者；Bearish BOS 只在 bullish Daily candles 中選擇 `high` 最高者。Doji 不作為來源；同低／同高時選擇時間較晚、較靠近 BOS 的反向 K。區間內沒有合格反向 K 時，該次 BOS 不建立 OB。
- `Show Daily OB BOS structure line` 預設開啟。Bullish 在被突破 confirmed swing high 價格，從該 pivot K 水平畫至 BOS K；Bearish 對稱使用被突破 confirmed swing low。BOS K 顯示方向標籤。此功能解釋 OB 所依據的 structure break，不參與 OB 成立、來源搜尋、上下界或失效判斷。
- Daily OB range 使用來源 K 的 Full Range `low → high`；同方向同一來源 Daily candle 最多一個 OB。Hybrid Range 不再用於 V10 Daily OB，V1／V4 舊架構不受影響。
- Daily FVG 使用三根完成 Daily candles 的標準 wick-to-wick gap，不設最小 gap 寬度；中間 candle 必須同方向且 body 至少為 Daily Wilder ATR(14) × 1.0。
- Bullish Daily OB/FVG 使用綠／黃；Bearish Daily OB/FVG 使用兩階淺紅；box 文字固定為 `D OB`／`D FVG`。
- Daily OB 只由完成 Daily close 穿越遠端失效：Bullish close 嚴格低於 bottom，Bearish close 嚴格高於 top；影線、未完成 intraday close 或收盤剛好等於邊界不使 OB 失效。Daily FVG 仍維持完成 Daily close 穿越 midpoint 失效；Midline 顯示預設關閉。
- Daily OB 與 FVG 每類最多保留 40 個；顯示開關不得停止底層 zone state 建立與失效。
- Daily zones 支援 Daily 與 intraday charts；高於 Daily 的 chart 只顯示 Weekly Bias，右上版本表標記 `USE D / INTRADAY`。
- 下一階段目標架構為 `Weekly Structure Bias → Daily OB/FVG + Daily MSS → H1 SETUP / ARMED / ENTRY`；本版不產生任何交易候選。
- V10 及後續新架構 build 必須在圖表最右上保留永久版本識別表。即使尚無統計，仍須顯示精確 build ID、目前 phase 與支援狀態；不得提供隱藏此表的開關。
- Weekly Bias 不再使用左側獨立表格。右上永久表的固定頂部順序為：BUILD、W BIAS、W SWING HIGH、W SWING LOW、W FLIPS；phase、timeframe 狀態與未來統計只能接在其下方。
- V4 保持舊架構穩定核對層，不同步 V10 的獨立 Weekly Bias。待 V10 的 Daily zones 與新 execution 完成視覺驗證後，再建立同一 V10 架構的獨立數值核對版本，不覆蓋 V4。

## V4 Top-down Model Research Engine（開發版）

- V4 必須是獨立 Pine 檔，不覆蓋 V3；V3 保留作為同一 Weekly context 下的 H4/H1/M30 比較基準。
- V4 固定使用 `1095D`（3 年）統計 Window，並固定載入 H1 chart；PRIMARY 直接使用完成的圖表 H1 bars，不透過 H4 data carrier 或 lower-timeframe arrays。
- V4 目前以 `PRIMARY W-D-H1` 作為台股正式核對模型：Weekly OB/FVG zone → Daily MSS bias → H1 execution。
- 原 `INTRADAY D-H4-H1` 與 `FAST H4-H1-M30` 暫時保留為 `LEGACY` 統計列，不參與本輪策略判斷或 V1 一致性驗收；是否重構於 PRIMARY 驗證完成後再決定。
- PRIMARY 內每個 Weekly zone 必須擁有獨立的 SETUP、ARMED、ENTRY state；不得以新的 zone touch 清除其他 zone 的候選。
- Weekly Zone Engine 採用與 V1 相同的多 Zone arrays：OB/FVG 每類最多 40 個、OB source 去重、OB 結構突破 candle 必須通過來源時框 ATR(14) × 1.0 displacement、OB 固定使用 Hybrid Range；FVG 使用標準三根完成 K 的 wick-to-wick gap，中間 candle 必須為同方向 ATR(14) × 1.0 displacement。每個 active zone 獨立判斷 H1 touch，並由 H1 close 穿越 midpoint 失效。
- PRIMARY 的 Daily MSS bias 使用與 V1 H1 chart 相同的完成 Daily candle 聚合、confirmed pivot、Daily close trend-reversal MSS 更新規則與固定結構失效位；SETUP、ARMED 與 ENTRY 則逐根回放完成 H1 bars。
- 相同 symbol、H1 chart、1095D、參數與資料覆蓋下，驗收對應為：V1 `SETUP` = V4 PRIMARY `SETUP`、V1 `ARMED` = V4 PRIMARY `ARMED`、V1 `Total` = V4 PRIMARY `Total`，且 TP2 Rate、Net R、Profit Factor、OB/FVG 與 replacement 分類都必須一致；任何差異都視為待追查。
- V1 與 V4 PRIMARY 必須共用同一份行為規格：Weekly Zone 建立與失效、Daily MSS Bias、1095D Window 邊界、per-zone touch transition、SETUP／ARMED／ENTRY lifecycle、expiry、Trade Plan 結果與績效公式都必須使用相同條件及執行順序。V1 可保留完整視覺物件，V4 可只保留統計；顯示差異不得改變訊號結果。
- 核心邏輯的修改順序固定為：先修改 V1、由使用者完成 TradingView 驗證，再將同一核心原樣移植到 V4，最後用固定標的核對所有共通欄位。V1 尚未通過前，不同步猜測性修改 V4。
- 1095D Window 的第一根 H1 是正式觀察起點，不做 Window 前 touch-state warm-up。若第一根 H1 已與有效同方向 zone 重疊，V1 與 V4 都將其計為 Window 內第一筆 touch；這是兩支程式共同的統計邊界定義，不視為額外污染。
- V4 使用與 V1 相同的統計名稱：SETUP replaced、ARMED replaced、OB SETUP、FVG SETUP、Same-zone SETUP、Changed-zone SETUP。`UNIQUE SETUP`、`U>A`、`A>T` 是 V4 額外研究欄位，沒有 V1 對應欄位。
- 完成的高週期 context 才能供低週期模型使用；不得將同一高週期 candle 的最終值回填至其內部較早 intrabars。
- Funnel 分開顯示 `Raw SETUP`、`Unique SETUP`、`Replaced`、`ARMED`、`TRADES`；轉換率使用 `Unique SETUP` 作為分母。
- `Unique SETUP` 定義為該 zone 沒有未完成流程時建立的新 episode；同 zone re-entry 替換尚未 ARMED 的流程時另計入 `SETUP replaced`。已 ARMED 的同 zone touch 不建立新 SETUP，也不計 replacement。
- 現行 per-zone `FULL` 已以 1504、2105、2324/H1 驗證 V1/V4 所有共通欄位一致；兩個 LEGACY 模型仍停用，不在本次驗收範圍。

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
- Bullish OB 為綠色；Bearish OB 保留顯示但統一使用淺紅色系；box 內顯示 `OB`。
- Bullish OB 在收盤價低於 midpoint 時停止向右延伸；Bearish OB 在收盤價高於 midpoint 時停止延伸。

## Fair Value Gap (FVG)

- 使用三根完成 Weekly candle：Bullish FVG 為第三根 low 高於第一根 high；Bearish FVG 為第三根 high 低於第一根 low。
- Gap 只要求符合標準 wick-to-wick 幾何條件，不使用價格百分比或 ATR 最小寬度門檻。
- 中間 Weekly candle 必須與 FVG 同方向，且 candle body 至少為完成週線 Wilder ATR(14) × 1.0：Bullish FVG 要求中間 candle 收紅／上漲，Bearish FVG 要求中間 candle 收黑／下跌。
- FVG 從確認該 gap 的 Weekly candle 開始繪製。
- Bullish FVG 使用較亮黃色；Bearish FVG 保留顯示但改用比 Bearish OB 更淡的淺紅色系，不再使用 olive/darker yellow；box 內顯示 `FVG`。
- Bullish FVG 在收盤價低於 midpoint 時停止延伸；Bearish FVG 在收盤價高於 midpoint 時停止延伸。

## CHOCH

- 使用獨立 pivot 系統，預設 swing length 2。
- Daily close 突破最新 pivot，且方向與已追蹤 trend 相反時產生 `CHOCH`。
- 線段由被突破的 Daily pivot candle 畫到確認突破的 Daily candle，不向未來延伸。
- CHOCH 只顯示線段，不顯示 `CHOCH` 文字，以降低圖表雜訊。
- Bullish 使用深綠、Bearish 使用深紅。

## MSS

- 使用獨立 pivot 系統，預設 swing length 4。
- MSS 不使用 ATR 或單根 candle-body displacement；較長 confirmed pivot、完成 Daily close 正式突破及 trend reversal 已構成完整條件。相同結構突破不得因由一根大 K 或多根中型 K 完成而得到不同 Bias。
- 每根完成 Daily candle 必須先使用該 candle 開始前已確認的 MSS pivot 判斷結構突破與事件，再寫入由本 candle 新確認的 pivot；不得先更新 pivot 再判斷突破，以免把當日 breakout target 移到較新的位置而漏掉 MSS。
- 線段範圍同 CHOCH，但 MSS 保留 `MSS` 文字；Bullish 使用亮綠、Bearish 使用亮紅。
- Daily CHOCH/MSS 線與文字只在 Daily chart 繪製；H4/H1/M30 仍以完成的 Daily candles 更新相同結構狀態與 SETUP Bias，但不顯示 Daily 結構物件，避免被誤認為目前圖表時框訊號。

## 顯示與資源限制

- OB/FVG 每類預設最多 40 個；CHOCH/MSS 線預設最多 120 個，超限時刪除最舊物件。
- `Stable replay extension` 預設開啟，使用 `extend.right`；midpoint invalidation 仍必須生效。
- 已移除 365D High/Low，以降低 Replay object pressure。
- Pine indicator 宣告上限為 500 boxes、500 lines、200 labels；這是平台物件上限設定，不是保證所有歷史區間皆可無限制顯示。

## SETUP（進場開發第一階段）

- SETUP 僅為圖上提示，不送出訂單，也不連接交易所或券商。
- 最新完成的 Daily bullish/bearish MSS 決定目前 bias。Bullish MSS 成立時固定保存當下最新 confirmed Daily swing low；Bearish MSS 成立時固定保存當下最新 confirmed Daily swing high，作為該 Bias 的結構失效位。
- 只有完成的 Daily close 跌破 Bullish Bias 失效位或突破 Bearish Bias 失效位時，Bias 才轉為 Neutral；失效位不隨後續 pivot 移動，也不使用 CHOCH 或時間期限取消 Bias。Neutral 後等待反方向 Daily MSS 建立新 Bias。
- 目前圖表 K 棒的 high/low 與仍有效、同方向的 Weekly OB 或 FVG 重疊時，視為進入 zone。
- 正式台股策略固定只做多：只有 Bullish bias 與 bullish zone 同時成立時建立綠色 `B SETUP`。Bearish OB/FVG 與 bearish Daily MSS/CHOCH 仍保留圖形與結構狀態，但不建立 `S SETUP`，也不進入 ARMED、ENTRY、Trade Plan 或統計。
- Bearish Daily MSS 仍會把 Bias 轉為 bearish，取消既有多方候選並阻止新多方 SETUP；後續需等待新的 Bullish Daily MSS 才重新允許多方流程。
- 同一次連續停留在 zone 內只顯示一次；離開後再次進入可重新顯示。
- 每個確切 Weekly zone 同時只保留一個尚未 ARMED 的 SETUP flow；重新進入同一 zone 時取代舊 flow，並刪除該 zone 先前的 SETUP 標籤，只顯示最新一個。不同 zone 的 SETUP flow 互不刪除。
- 每個有效 Weekly zone 都有獨立的 SETUP → ARMED → ENTRY 流程；同一根 H1 可同時為多個重疊 OB/FVG 建立 SETUP，後續也可各自形成 ARMED、ENTRY 與 Trade Plan。
- 每個 zone 同時最多一條未完成流程。連續接觸不重複建立；至少一根 H1 完全沒有接觸該 zone，之後再次進入才算 re-entry。
- Re-entry 只替換仍停在 SETUP 的同 zone 流程；若該 zone 已 ARMED，新的 touch 不建立 SETUP，也不取消 ARMED。
- 每個確切 Weekly zone 最多只能建立一筆有效 Trade Plan。有效 ENTRY 成功建立 Trade Plan 時，該 zone 立即標記為 traded／consumed；不等待 TP／SL 結果。之後價格再次進入同一 zone，不再建立 SETUP，不論原交易仍進行中或最終為 WIN／LOSS。
- 尚未形成有效 Trade Plan 的 SETUP expiry、ARMED 失效或無效 ENTRY 不消耗 zone；之後離開再進入仍可建立新 SETUP。不同 key 的重疊 OB/FVG 各自保有一次交易機會。
- SETUP expiry 固定預設 15 根 H1，約三個台股交易日；到期只取消尚未 ARMED 的流程。
- Zone 失效時取消該 zone 尚未完成的 SETUP／ARMED／ENTRY 候選；未成交流程的 SETUP 標籤可保留到同 zone 下一次 SETUP 取代。有效 Trade Plan 建立後，該筆 SETUP／ARMED／ENTRY 標籤封存為完整歷史鏈，不得再被同 zone 後續 touch 取代；已完成 Trade Plan 的歷史與統計保留。
- 每個確切 zone 最多顯示一個 SETUP 標籤，全部 zone 合計仍受 `Maximum SETUP labels` 上限控制；刪除或裁切標籤不影響訊號判定與累計統計。

## ARMED（進場開發第二階段）

- 每個 SETUP 建立一個等待中的 ARMED 候選；新的 SETUP 會取代前一個尚未完成的候選。
- 重新進入同一 zone 時，已完成的歷史 ARMED 標籤保留；只有尚未完成的 ARMED 候選被新 SETUP 取代。
- 使用 H1 confirmed pivot，預設 swing length 3。SETUP 建立時立即保存當下最後一個同方向 break pivot：Bullish 保存最後 confirmed swing high，Bearish 保存最後 confirmed swing low；等待期間不因新 pivot 出現而移動 break level。
- 突破必須發生在 SETUP 之後，由前一根 H1 收盤尚未越過、目前 H1 收盤正式越過固定 break level；不再另加 ATR candle-body displacement。
- Long-only 正式流程成立時只顯示 `B ARMED`，同一 SETUP 最多一次，不畫水平線。
- ARMED 成立時，將同一 zone 對應的最新 SETUP 標籤改為較暗、較透明並封存。若流程未形成有效 Trade Plan，後續同 zone 新 SETUP 可刪除並取代該封存標籤；若已形成有效 Trade Plan，標籤固定保留為完整交易鏈的一部分。
- SETUP 所屬 Weekly zone 失效、出現反向 Daily MSS，或等待超過預設 15 根 H1 時取消候選；ARMED 不另設第二套 expiry。
- ARMED 標籤最多保留最新 40 個，可由 `Maximum ARMED labels` 向下調整。

## ENTRY（進場開發第三階段）

- ARMED 成立時保存被突破的 pivot level、來源 zone、ARMED bar，以及反方向最近 confirmed pivot 作為保護 swing。
- ENTRY 必須發生在 ARMED 之後的 K 棒；Bullish 為 low 回到或跌破突破位且收盤重新站上，Bearish 為 high 回到或突破突破位且收盤重新跌回其下。
- 每個 ARMED 最多產生一個 `B ENTRY`，只顯示小標籤，不畫水平線。
- 有效 Trade Plan 建立成功時，來源 zone 立即標記為 traded／consumed；同一 zone 後續不再產生 SETUP、ARMED、ENTRY 或第二筆 Trade Plan。
- 原 zone 失效、Daily MSS bias 反向、收盤突破反方向保護 swing，或出現新 SETUP 時取消 ENTRY 候選。
- `ENTRY retest expiry bars` 預設為 15 根 H1；等待超過 15 根 H1 後取消尚未 ENTRY 的候選，但不消耗來源 zone。輸入設為 0 時仍可關閉此期限。
- ENTRY 標籤最多保留最新 40 個，可向下調整；SETUP、ARMED、ENTRY 三類合計設定上限為 120，低於 indicator 的 200 labels 宣告上限。
- `Show SETUP`、`Show ARMED`、`Show ENTRY` 僅控制各階段標籤顯示，不改變狀態判定與後續流程。
- V1/V4 `Show SETUP/ARMED/ENTRY statistics` 預設開啟。V1 關閉時隱藏結果表的 `SIGNAL FUNNEL`，保留 Total、Open、Win TP2、TP1→BE、Direct Loss、TP1/TP2 Rate、Net R、Avg R 與 Profit Factor；V4 關閉時只保留 MODEL、Total、TP2 Rate、Net R 與 Profit Factor，coverage 仍併入 MODEL 的 `FULL/PART`。此開關只影響表格顯示，不影響圖上標籤、訊號判定、交易追蹤或累計值。
- ENTRY 標籤本身不代表實際送單；Stop/TP 的圖表追蹤由下方 Trade Plan 階段處理。

## 交易統計與週期比較

- V1 不提供 `Entry timeframe` 選項，正式研究入口固定為 H1 chart；只有 H1 直接計算 SETUP/ARMED/ENTRY，其他圖表不建立新候選並顯示 `Use H1 chart`。
- V1/V4 PRIMARY 的 SETUP、ARMED、ENTRY、Trade Plan、Total、勝率、Net R、Profit Factor 與所有 funnel／來源分類只統計多方流程；空方 zone 與 Daily structure 僅供圖形與風險 context。
- 統計期間由 `Statistics lookback days` 控制，可選 90、180、365、730 天；期間開始前不建立候選交易。
- `SETUP expiry H1 bars` 預設 15；只適用於尚未 ARMED 的 SETUP。
- 預設 TP1 平倉比例為 50%；TP1 達成後，剩餘部位的 SL 移到 Entry。預設 TP1=1R、TP2=2R 時，WIN TP2=+1.5R、TP1→BE=+0.5R、Direct Loss=-1R。
- 同一根 K 同時觸及 SL/TP 時維持 SL 優先。
- V2 只在 M30 圖表計算；內部分別以 M30、完成 H1 K、完成 H4 K 維護三套獨立 SETUP/ARMED/ENTRY 與交易結果，表格僅做比較顯示。
- V3 Cross-Timeframe Stats 以完成的 M30 bars 作為唯一基礎資料流；M30 圖表直接逐 bar 計算，H1/H4 圖表使用 `request.security_lower_tf()` 取得每根圖表 K 棒內依時間排序的 M30 intrabars 並逐筆回放。
- V3 必須由 M30 基礎資料流分別驅動 M30、完成 H1 K、完成 H4 K 三套獨立 SETUP/ARMED/ENTRY 與交易狀態，表格固定顯示 M30、H1、H4 三列。圖表週期不得改變任何一列結果；非 M30/H1/H4 圖表顯示切換提示，不宣告支援。
- V3 只納入已完成的 M30 bars；即時尚未完成的 M30 bar 不得提前計入。若 TradingView intrabar 歷史覆蓋不足，表格必須顯示資料覆蓋警告，不能把部分歷史結果標示為完整同步。
- V1、V3 與 V4 現行統計 Window 均強制固定為 1095D；V1/V3 不再顯示 Window 選項。V3 可在 M30/H1/H4 執行，V1 與 V4 PRIMARY 固定使用 H1 chart。
- V3 為純統計版本，不建立 Weekly zone、CHOCH/MSS、SETUP/ARMED/ENTRY 或 Trade Plan 的 box、line、label；上述規則仍以純數值狀態驅動 M30/H1/H4 三套統計。逐筆視覺檢查使用 V1。
- V3 必須分別檢查 M30、H1、H4 的第一筆可用資料是否涵蓋 Window 起點；三列全部通過才顯示 `3TF V3 FULL`，否則顯示 `3TF PARTIAL` 並在列名標記 `PART`。

## Trade Plan（進場開發第四階段）

- ENTRY 成立時以確認 K 收盤作為 Entry，ARMED 保存的反方向 confirmed pivot 作為 SL；Bullish SL 必須低於 Entry，Bearish SL 必須高於 Entry，且距離至少一個 `syminfo.mintick`。
- Risk 定義為 `abs(Entry - SL)`；TP1 預設 1R、TP2 預設 2R。若使用者將 TP2 倍數設得低於 TP1，實際 TP2 自動採用 TP1 倍數作為下限。
- 每筆計畫建立 SL、TP1、TP2 三條短線與一個資訊標籤；線段由 ENTRY bar 開始，逐 bar 延伸到計畫結束。
- SL/TP 從 ENTRY 下一根 K 才開始判定，避免使用 ENTRY 確認 K 已發生的 high/low。
- 同一根 K 同時觸及 SL 與任一 TP 時，採保守的 SL 優先；TP2 觸及標示 `WIN TP2`，原始 SL 觸及標示 `LOSS`，若先前已達 TP1 且之後觸及 Entry 則標示 `TP1 → BE`。
- TP1 達成後，剩餘部位的 SL 立即移到 Entry，繼續等待 BE 或 TP2。首次觸及 TP1 的同一根 K 若也觸及原始 SL，因無法判定盤中先後，仍依 SL 優先記為 Direct Loss。
- 新 SETUP/ARMED/ENTRY 與原 Weekly zone 後續失效均不取消已建立的 Trade Plan；每筆計畫獨立追蹤。
- 最多保留最新 20 筆 Trade Plan，超限時整組刪除最舊的三條線與資訊標籤。
- Trade Plan 只供圖表分析，不使用 `strategy.entry()`，也不會實際送單。
- V1 的 `Show SL/TP trade plans` 只控制 SL/TP lines 與 plan labels；有效 ENTRY、交易狀態與績效統計永遠計算，不受顯示開關影響。
