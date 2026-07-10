# TradingView SMC Replay Toolkit - Development History

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
