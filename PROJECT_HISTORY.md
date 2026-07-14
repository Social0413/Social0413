# TradingView SMC Replay Toolkit - Development History

## 2026-07-14 - Per-zone engine rollback and debugging discipline

Per-zone SETUP/ARMED/ENTRY 同時加入 V1 與 V4 後，兩個指標在 H1 出現完全不顯示的問題。第一次處理錯誤地把重複搜尋與 touch-state 結構視為已確認根因，建立 `V1-PZ-02 / V4-PZ-03` 並預設啟用 `FULL`；TradingView 驗證證明問題仍存在，因此該嘗試已撤回。

目前回到可驗證基準：V1 `V1-PZ-01 / PZ OFF`、V4 `V4-PZ-02 / PZ OFF`。1504、2105、2324 的 H1 均確認兩者可以同時顯示。後續先只測 V1 `TOUCH`，再測 V1 `FULL`；找出確切失敗階段並完成 V1 後，才同步 V4。版號與 diagnostic mode 必須保留在表格標題，避免截圖與程式版本無法對應。

本次事件也建立固定收尾規則：每段開發對話結束前，必須記錄錯誤假設、失敗修改、rollback、驗證證據與可重用教訓，並更新對應 MD。標準流程見 `CLOSEOUT_CHECKLIST.md`。

正式收尾時進一步整理全部 Repository MD：README 成為現況與閱讀入口，TODO 只保留可執行工作，Known Bugs 只保留現行問題，Roadmap 只保留中長期方向；Spec、Design、Changelog、Test 明確區分 per-zone 目標、草稿與已驗證基準。

> 本文件保留開發歷程；現行可執行規格請以 [SMC_SPEC.md](SMC_SPEC.md) 為準，設計決策見 [DESIGN.md](DESIGN.md)，未驗證事項見 [TEST_RESULT.md](TEST_RESULT.md) 與 [KNOWN_BUGS.md](KNOWN_BUGS.md)。

本文記錄目前專案的發展過程與重要決策。

## 1. 基礎 TradingView 技能

一開始先建立兩個基礎技能：

- `open-tv`：開啟 TradingView Desktop。
- `close-tv`：關閉 TradingView Desktop。

這兩個技能原本在 Codex 技能目錄中，後來移到目前工作資料夾並納入版本控管。

## 2. 建立開圖技能 open-tv-symbol

接著建立 `open-tv-symbol`。

目標是讓 Codex 可以理解：

```text
ETH H4
```

並轉成：

```text
BINANCE:ETHUSDT
interval=240
```

測試過程中發現 `tradingview://chart/...` 有時會被 Windows 回報 `Access is denied`，或 TradingView Desktop 不一定切換圖表。

因此補上較穩的 fallback：

```powershell
Start-Process 'https://www.tradingview.com/chart/?symbol=BINANCE%3AETHUSDT&interval=240'
```

## 3. 嘗試建立 TradingView 版面技能

曾建立 `create-tv-layout`，目標是自動建立 `CODEX_YYYYMMDD` 版面。

後來因 TradingView Desktop UI 在目前 Codex 環境中不可見，無法穩定操作「管理版面」UI，因此刪除該技能。

## 4. 建立 SMC 指標技能

建立第四個技能：

```text
smc-weekly-ob-fvg
```

初始目標：

- 使用目前 ETHUSDT。
- 畫高時框週線 OB。
- 畫高時框週線 FVG。
- 看多 OB 用綠色。
- 看空 OB 用紅色。
- OB 有效範圍延伸到價格突破中線。

## 5. 第一版 Pine Script

第一版 Pine Script 使用 `request.security()` 取週線資料。

後續問題：

- 有些版本在週線有圖，但切到日線或小時線會消失。
- 有些版本在 TradingView 回放模式下，每前進一天整個指標會消失。
- 嚴格結構突破版 OB 太少或不顯示。
- 敏感 displacement 版 OB 又太多。

## 6. 週資料聚合改寫

為了讓週線區塊在日線與小時線也能保留，改成在目前圖表中自行累積每週 OHLC，不完全依賴 weekly `request.security()` 歷史索引。

這讓跨時框顯示更可控，但也帶來回放模式下 box/line 物件壓力問題。

## 7. 365 天高低點

新增 365 天高低點：

- 高點標記為 `365D High`。
- 低點標記為 `365D Low`。
- 線段固定錨定在發生高/低點的那根 K 棒。
- 往右延伸直到高低點改變。

曾出現垂直線問題，後來改成水平 ray 方式。

## 8. FVG 條件

FVG 加入過濾條件：

- FVG 區間必須大於價格中位數的 `3%`。

目的：

- 避免太多細小 FVG。
- 讓圖表更適合高時框分析。

## 9. OB 判斷收斂

目前 OB 回到「結構突破」版本：

- Bullish OB：週收盤突破近期結構高，回找最近 bearish 週 K。
- Bearish OB：週收盤跌破近期結構低，回找最近 bullish 週 K。

並加入去重：

- 同一方向、同一根週 K，只能畫一次 OB。

這是為了避免同一 OB 區域重複生成，造成特別亮或視覺疊加。

## 10. 回放模式穩定化

反覆測試後確認：

- 月時框 replay 較穩。
- 日線與週線 replay 仍可能讓整個指標消失。

推測原因：

- D/W 回放每前進一步的重算頻率與 box/line 物件壓力較高。
- 月線重算次數少，所以不容易觸發整個指標消失。

目前採取：

- 最大區塊數從 220、180、80 最後降到 40。
- 365D 高低點掃描從 5000 根降到 370 根。
- 移除 Debug label。
- OB/FVG 來源去重。

## 11. GitHub 版本控管

建立 Git repo 並推送到：

```text
https://github.com/Social0413/Social0413
```

重要 commit：

- `dbbb5e3` Initial TradingView Codex skills
- `f54c516` Improve SMC replay drawing behavior
- `9d7eb95` Stabilize SMC replay extension
- `ef34219` Revert replay retention toggle
- `e17634d` Add replay safe SMC display mode
- `ca6a41a` Restore midpoint invalidation and reduce replay load
- `b18bd39` Remove SMC debug label and cap zones
- `85927db` Prevent duplicate OB source zones

後續規則：

- 每次完成修改後直接 commit 並 push。
- 除非使用者明確要求不要推送。

## 12. 目前下一步候選

若日線/週線 replay 仍整個消失，下一步不再只調整現有 box/line 邏輯，而應建立回放專用輕量版本：

- 僅保留最近 N 個有效 OB/FVG。
- 更少 box/line 物件。
- 或將部分顯示改成 plot/fill 形式，降低 TradingView replay 的物件壓力。

## 13. 日線 CHOCH/MSS 與移除 365D

新增日線級別結構標記，後續調整成由日線資料內部直接產生事件，避免外層判斷過於保守而看不到標記：

- 使用日線 high/low 突破 confirmed swing high/low 判斷結構突破。
- 若突破方向反轉既有日線趨勢，標記為 `D CHOCH`。
- 若突破方向延續或啟動日線趨勢，標記為 `D MSS`。
- 後續改用 `plotshape` 繪製日線結構標記，避免增加 replay 模式的 label 物件壓力。

同時移除 365D 高低點：

- 刪除 `365D High` / `365D Low` 線與 label。
- 刪除 365 天歷史掃描邏輯。
- 目標是降低回放模式與長歷史圖表的資源壓力。

## 14. CHOCH/MSS 顯示修正與 FVG 黃色系

使用者回報設定中已開啟 `Show daily CHOCH/MSS`，但圖表上看不到標記，因此將日線結構判斷改為：

- 在 daily `request.security()` 內部直接追蹤 swing、trend 與突破事件。
- 日線 high 突破 confirmed swing high 時標 bullish `D MSS` 或 `D CHOCH`。
- 日線 low 跌破 confirmed swing low 時標 bearish `D MSS` 或 `D CHOCH`。
- 外層只負責接收日線事件並繪製標記。

使用者再次回報 CHOCH/MSS 仍未顯示後，將日線結構顯示從 `label.new` 改成 `plotshape`：

- Bullish CHOCH/MSS 顯示在 K 棒下方。
- Bearish CHOCH/MSS 顯示在 K 棒上方。
- 移除 `Maximum daily structure labels`，因為不再使用 label 物件。

## 15. CHOCH/MSS 改為水平結構線

使用者明確要求不要用 shape，而是用水平線標示，因此將日線 CHOCH/MSS 輸出改為：

- 當日線 high 突破 confirmed swing high，在該 swing high 價格畫水平線。
- 當日線 low 跌破 confirmed swing low，在該 swing low 價格畫水平線。
- 方向反轉標為 `D Bull CHOCH` 或 `D Bear CHOCH`。
- 方向延續或啟動趨勢標為 `D Bull MSS` 或 `D Bear MSS`。
- 在週線、月線等高於日線的圖表，嘗試用 daily lower-timeframe events 抓出該週/月內的日線結構事件。
- 保留水平線與小型文字標籤，讓結構位階比 shape 更容易看見。

使用者再次截圖確認日線圖上仍看不到 CHOCH/MSS 後，改成：

- 日線圖直接用目前圖表的日線 K 棒找 pivot swing、判斷突破、畫水平線。
- 不再讓日線圖依賴 `request.security()` 回傳 daily event。
- 週線、月線才使用 lower-timeframe daily events。

## 16. 參考 03_H4M15 重做 CHOCH/MSS

使用者指出應直接參考 `C:\30_CodeX\03_H4M15` 既有寫法，而不是重新猜測。前面寫錯的原因：

- 沒有先搜尋既有可用實作，直接自行設計 daily event system。
- 把 CHOCH/MSS 混成同一套事件碼，還加入 `request.security_lower_tf()`，造成顯示流程過度複雜。
- 把 MSS 誤寫成「延續或啟動趨勢」也能成立，和 `03_H4M15` 的實作不一致。

修正後：

- CHOCH 使用獨立短 swing pivot，預設長度 2。
- MSS 使用獨立長 swing pivot，預設長度 5。
- MSS 另外要求 candle body 滿足 ATR displacement filter。
- 只有突破方向與 tracked trend 相反時才觸發 CHOCH/MSS。
- 保留使用者要求的水平線輸出，線位畫在被突破的 pivot 價位。

同時調整 FVG 顏色：

- Bullish FVG 改為較亮黃色。
- Bearish FVG 改為較暗橄欖黃。

## 17. CHOCH/MSS 線段顯示收斂

使用者確認 CHOCH/MSS 已出現，但線太長、標籤太亂，因此調整顯示規則：

- CHOCH 改成暗色系。
- MSS 改成亮色系。
- CHOCH/MSS 不再建立獨立 label。
- 由於 Pine `line` 物件本身不能像 TradingView 手動畫線一樣直接內建文字，改用透明 text box 對齊線段中間，視覺上讓 `CHOCH` / `MSS` 在水平線中間。
- 結構線不再無限延伸；建立後從下一根 K 開始，只要 K 棒 high/low 觸碰該線位，就把右端點停在觸碰 K 棒。

## 18. 固定日線判斷 CHOCH/MSS

使用者確認本專案的時框分工：

- 週時框判斷 OB、FVG。
- 日時框判斷 CHOCH、MSS。

上一版雖然沒有使用 M15 資料，但 CHOCH/MSS 實際上跑在目前圖表週期上；在日線圖就是 D，在其他週期就不是 D。這不符合規格。

修正後：

- CHOCH/MSS 判斷固定包進 `request.security(syminfo.tickerid, "D", ...)`。
- 保留 `03_H4M15` 的 CHOCH/MSS 內部邏輯，但資料來源強制為日線。
- OB/FVG 維持週線來源。

後續發現完全包進 `request.security("D")` 後，日線圖本身可能不顯示 CHOCH/MSS。因此再調整：

- 在 D 圖上直接用目前圖表的日線 K 棒跑同一套 `03_H4M15` 結構邏輯。
- 在非 D 圖上才使用 `request.security(..., "D", ...)`。
- 這樣仍維持「D 判斷 CHOCH/MSS」，但避免 D 圖被 security 封裝吃掉訊號。

再度回測後，發現只要引入 `request.security("D")` 分支就會讓日線顯示不穩。最終改回 `64482af` 這個已知可顯示版本的 CHOCH/MSS 邏輯，只增加 `timeframe.isdaily` 限制：

- 日線圖顯示 CHOCH/MSS。
- 非日線圖不畫 CHOCH/MSS，避免誤用 M15/H4。
- 不再用 `request.security("D")` 產生 CHOCH/MSS。

使用者接著要求日線畫出的 CHOCH/MSS 在其他時框也要顯示，但不能用其他時框重新判斷。因此改成：

- D 圖保留 `64482af` 的可顯示直接邏輯。
- 非 D 圖只透過 `request.security(..., "D", ...)` 讀取日線訊號並顯示。
- 顏色規則固定為：做多綠色系、做空紅色系；CHOCH 較暗、MSS 較亮。

### 2026-07-10 - D CHOCH/MSS intraday display fix

After testing showed D CHOCH/MSS only appeared on the daily chart, the non-daily display path was changed to match the working OB/FVG approach: intraday bars now aggregate into completed daily candles first, then the daily CHOCH/MSS logic runs from those completed daily candles. This removes the unstable `request.security("D")` display path and prevents H4/M15 from being used as the structure source.

### 2026-07-10 - CHOCH/MSS structure-break segment direction

CHOCH/MSS lines were changed from future-facing extension lines into fixed structure-break segments. Each line now starts from the broken daily pivot candle and ends at the candle that confirms the break, with the text placed below the line for readability.

## 2026-07-13 - OB displacement and Hybrid Range

使用者以兩組圖表指出多個紅色／綠色 OB 難以從價格行為直覺解釋。共同原因是弱結構突破也會建立 OB，以及 Full wick 使長影線來源 K 形成過寬 Zone。規則因此收斂為兩項：結構突破 candle body 固定至少為來源時框 Wilder ATR(14) × 1.0，並取 searchback 內該 displacement 前最近的反向 candle；OB 固定使用保留遠端 wick 的 Hybrid Range。V1 與 V4 使用相同公式，FVG、midpoint invalidation 與其他交易流程本次不變。

## 2026-07-13 - FVG ATR filtering

FVG 規則依相同的簡單化原則收斂：移除固定 3% 門檻，改為 gap 至少為來源時框 Wilder ATR(14) × 0.5；中間 candle 必須與 FVG 同方向且 body 至少為 1 ATR；確認 candle close 必須位於自身 range 的順向半部。原先討論的「確認 close 保持在 gap midpoint 外側」因三 candle gap 定義必然成立，實作交叉檢查時改為可實際過濾的 candle range half 條件。V1 與 V4 使用相同公式，midpoint invalidation 與其他交易流程本次不變。

實圖回歸後發現上述版本排除大量肉眼可辨識的標準 FVG，尤其 2105 與 1504 的 FVG SETUP 大幅下降。規則因此再次簡化：保留標準三根完成 K 的 wick-to-wick gap，以及同方向、body 至少 1 ATR 的中間 displacement；移除 ATR × 0.5 gap 寬度與確認 K 順向半部條件。該過嚴版本的三標的結果僅保留為歷史對照。
