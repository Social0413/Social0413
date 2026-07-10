# TradingView SMC Replay Toolkit

這個專案整理了一組給 Codex 使用的 TradingView 自動化技能，以及一個用於 SMC 回放分析的 Pine Script 指標。

專案目標是讓 Codex 可以協助開啟 TradingView、切到指定標的與週期，並用穩定、可版本控管的方式迭代 SMC 分析工具。

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
- FVG 區間必須大於價格中位數的 3%。
- OB 使用結構突破規則：
  - 週收盤突破近期結構高，回找最近 bearish 週 K 當 bullish OB。
  - 週收盤跌破近期結構低，回找最近 bullish 週 K 當 bearish OB。
- 同一方向、同一根週 K 只能生成一次 OB。
- OB/FVG 過中線後停止延伸。
- OB/FVG 文字永遠顯示。
- 額外用水平線標記日線級別的 CHOCH 與 MSS，邏輯參考 `C:\30_CodeX\03_H4M15`，將 M15 結構判斷改成 D。
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
