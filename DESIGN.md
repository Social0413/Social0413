# 設計說明

> 目前狀態：V1 `V1-LONG-01`／V4 `V4-LONG-01` 已在 2105、2324/H1 完成 Long-only 共通統計對齊；midpoint invalidation 未修改。

## V10 Weekly Structure Bias

- V10 是新的策略架構分支，使用 `smc_weekly_structure_bias_v10.pine`，不覆蓋 V1 視覺基準或 V4 統計基準。
- `V10-FVG-03` 同時使用 canonical confirmed-Weekly Bias 與 canonical confirmed-Daily zones；保留 FVG-01 的 FVG K1/K2/K3 時間身分、K2 ATR alignment 與 canonical Daily close-time zone endpoint，獨立最小 gap ATR multiplier 固定為 0.50 並已通過 2105／Daily 視覺過濾；不恢復 Weekly OB/FVG、歷史 K3 half-range filter 或 legacy execution。
- `canonicalEthTickerId = ticker.modify(syminfo.tickerid, session.extended)`是 V10 唯一高週期 request symbol。Weekly 與 Daily requests 都從此 ETH ticker 取得資料，不再繼承圖表當下的 RTH／ETH；因此使用者切換圖表 session 不會改變 canonical Bias、BOS、OB/FVG event 或 geometry。
- 原生 chart bars 由 TradingView 控制，Pine 無法替使用者切換 session。`syminfo.session`只用於右上 SESSION 驗收列：intraday ETH 顯示 `ETH`，RTH／其他 session 顯示 `USE ETH (...)`；Daily 以上顯示 `SOURCE ETH`。
- `canonicalWeeklyBiasEngine()` 在 Weekly request context 依序判斷進入本週前已確認的 swing high／low突破、更新 Bias／flip counts，再發布本週新確認的 pivot；規則順序與 `V10-DZONE-04` 前相同。
- `confirmedWeeklyBiasSnapshot()` 對 time、Bias、swing high／low、Bull/Bear flips 與兩個 flip events 全部使用 `[1]`；外層 `lookahead_on` 只發布上一根已完成 Weekly snapshot。
- Daily／H4／H1 不再保存 chart-local Weekly OHLC arrays 或 Bias state。W BULL／W BEAR marker 只在 canonical Weekly time 改變後的第一根 chart bar顯示一次；背景、steplines 與右上表直接讀取相同 canonical state。
- `V10-WBIAS-02` 曾將獨立方向表放在 `position.middle_left` 避開商品資訊；`V10-DZONE-02` 已取消這張表，方向與結構數值統一放入 `position.top_right` 永久表。
- confirmed swing high／low以 stepline 顯示；Bias 真正改變時在確認可用的第一根圖表 bar 顯示 `W BULL／W BEAR`，並可選擇顯示極淡的方向背景。
- Swing steplines 因長歷史畫面容易與 OB/FVG 重疊，從 `V10-WBIAS-03` 起預設關閉；使用者需要驗證結構門檻時仍可手動開啟。背景維持預設開啟。
- Weekly/H1 因載入歷史深度不同，累計 Bias flip 次數可以不同；目前方向與最新 confirmed swing levels 才是跨時框一致性的主要驗收項目。
- High timeframe 不是 `W` 時，V10 Bias 表顯示 `USE W SOURCE`，不把其他來源時框誤標為 Weekly Bias。
- `canonicalDailyZoneEngine()` 在 Daily request context 逐根計算 Wilder ATR、confirmed OB pivot、一次性 BOS、來源 K 與 FVG；FVG 的 K2 body 固定比較 `Daily ATR(14)[1]`，gap width 固定至少為 `max(K2 ATR × 0.50, syminfo.mintick × 2)`，並輸出 K1 first time、K2 displacement/source time、K3 confirmation/event time。`confirmedDailyZoneSnapshot()` 對全部輸出使用 `[1]`，外層以 `lookahead_on` 取得上一根已確認 Daily event 與該 candle 的 canonical `time_close`，避免 developing Daily values 與 historical/realtime 差異。
- Daily/H1 chart 不再維護各自的 Daily OHLC、ATR 或 pivot arrays；兩者只在 `canonicalDailyTime` 改變時消費一次相同 snapshot。處理順序固定為：完成 Daily close 失效既有 zones → 建立 snapshot 的 OB → 建立 snapshot 的 FVG。
- Canonical request 只傳回 scalar event／geometry，不傳回 box、line 或大型 collections；繪圖與 zone 平行 arrays 留在 chart context，以控制 request memory 與 Replay object lifecycle。
- Daily OB 使用獨立 pivot length 4，並以 broken-pivot time 記錄每個 confirmed swing 已被消耗；Bullish／Bearish BOS 分別要求前一日 close 尚在結構內、目前完成 close 首次穿越尚未消耗的 confirmed swing。只有該首次 BOS 的突破 K 方向一致且 body 至少為 Daily ATR(14) × 1.0 才建立 OB；弱突破不建立，也不允許同 pivot re-cross 補建。
- OB source candidate 嚴格追蹤 pivot K 與 BOS K 之間的反向 K。新 pivot 發布時，先掃描 pivot 後至確認當日的固定 pivot-confirmation 區間初始化候選；後續完成 Daily candle 再逐根更新。Bullish 保存 `low` 最低的 bearish K，Bearish 保存 `high` 最高的 bullish K；比較使用 `<=`／`>=`，因此同價由較晚 candle 取代。BOS 判斷與 source 讀取先於本 candle candidate 更新，確保 BOS K 不會進入候選。Zone 使用選中來源 K 的完整 `low → high`。
- OB 建立後可選擇建立 chart-context BOS structure line：canonical snapshot 提供 completed Daily BOS time／high／low，以及當次被突破 confirmed pivot 的 time／price；line 固定在 pivot price，從 pivot K 水平畫至 BOS K，並在 BOS K 建立方向 label。Objects 獨立限量，不回寫 canonical engine，也不影響 OB source 或 zone active／invalidation state。
- BOS objects 另以 `direction + canonical BOS time` 保存 event key。`addDailyObBosLine()` 先檢查 key，只有尚未存在時才同時建立 line 與 label；line、label、key 的 shift 必須保持相同生命週期。Broken-pivot time／price 是座標，不作為重複 event identity。
- Daily OB 由完成 Daily close 穿越遠端 top／bottom 失效；Daily FVG 保留 midpoint invalidation，且不加入額外 mitigation state。兩者都不因影線或未完成 intraday candle 失效。Box／midline 停止延伸的右端固定使用 canonical completed-Daily `time_close`，不使用 Daily／H4／H1 各自的 chart bar `time`。
- Daily zones 使用單一平行 arrays 保存 box、midline、type、direction、midpoint、active、source time、event time、FVG first time、top、bottom；OB 的 source time 是來源反向 K、event time 是 BOS K，FVG 的 source time 是 K2、event time 是 K3、first time 是 K1。每類超過上限時刪除最舊同類物件與 state。
- Daily zone 顯示開關只決定是否建立 box／midline handle；OB/FVG state 仍照常建立，避免未來 execution 被顯示設定改變。
- `V10-DH1-SETUP-02R1` 是目前已驗收的 SETUP 基準：zone 平行 arrays 維持 stable zone key、SETUP label、tracking flag、tracked low 與前一根 H1 overlap state，並保存永久 `setupUsed`。SETUP state 與 zone type／direction／geometry 使用相同索引生命週期；顯示關閉時仍照常追蹤與累計。
- ETH H1 completed bar 逐 zone 計算 raw overlap。Weekly Bias 為 Bullish且 Bullish zone 由未重疊轉為重疊時啟動 episode；tracking 期間只在新 H1 low 低於 tracked low 時移動同一 marker。H1 close 離開上下界、Weekly Bias 轉空／中性或 zone inactive 時停止 tracking。
- 第一次有效 SETUP 時立即設定 `setupUsed = true`；new-entry gate 必須同時要求 `not setupUsed`。同一段停留的 lower-low update 不增加 count；離開後只停止 tracking，re-entry 不移動 marker也不建立新 episode。
- `V10-DH1-ARMED-02` 為每個 zone 增加 setup bar、break level、獨立 `waitingForArmed`、ARMED marker、armed／active flag、ARMED bar 與 frozen SETUP low，並與既有 zone arrays 維持相同 push／remove／trim 索引生命週期。H1 confirmed swing high 使用 length 3；SETUP 建立時快照一次，之後只在 tracking 中的 low 再創低時重新快照，不逐 pivot 追高。
- SETUP 一成立即把 `waitingForArmed` 設為 true。Tracking 的 completed-H1 順序為 hard invalidation → lower-low／break snapshot → 所有 waiting candidates 的 close crossover → 未成立才處理 close 離開 zone。離開 zone只把 tracking 設為 false並凍結 low／break，不能清除 waiting state；re-entry 不恢復 tracking。
- Pre-ARM candidate使用Weekly Bias、Daily zone及frozen-low strict close break失效，沒有時間expiry。ARMED後清除waiting、暗化SETUP marker並保存frozen low與ARM high；兩者midpoint成為pending Buy Limit。ARM後只由Weekly Bias改變撤單，不再讀Daily zone或frozen-low失效。
- `V10-DH1-ARMED-03` 在每個 zone 的平行 arrays 新增 break line handle。預設關閉；開啟時 `updateArmBreakLine()` 在 SETUP／lower-low snapshot 建立或移動水平 dotted line，`stopArmBreakLine()` 在 hard invalidation、frozen-low invalidation或 ARMED transition停止延伸，zone trimming 時與其他 state 一起刪除。此 handle 不回寫 break level或 candidate state。
- `V10-DH1-ARMED-03`為ARM階段收尾基準；`V10-DH1-ENTRY-03`沿用ENTRY-02的frozen SETUP low、ARM high、entry limit level、limit line與`entryTriggered`。Zone trimming跳過尚未成交的ARMED，避免Daily zone物件上限意外撤單。
- V10 ENTRY從ARM下一根completed ETH H1起檢查`low <= midpoint limit`，觸價後以固定limit成交。獨立Trade Plan arrays保存Entry、final SETUP low下方2 ticks SL、1R／2R、起始bar、狀態與三條線；狀態0為TP1前、1為TP1後SL移到Entry、2為TP2、-1為Loss／BE結束、-2為無效SL診斷。成交bar立即進入`SL → TP2 → TP1`保守判定；同bar SL+TP衝突另以紫紅marker標示。
- 右上永久表前三欄為label／OB／FVG；頂部全域列將兩個來源欄合併，來源區則分別顯示SETUP、ARMED、ENTRY、TP1、TP2、BE、SL與NET R。SETUP／ARMED／ENTRY在per-zone transition直接依`zoneType`累計；交易結果從獨立Trade Plan保存的`tradeZoneTypes`回寫，裁切視覺arrays不回退累計。
- V4 不接收 V10 中間階段修改。V4 繼續代表已驗證的 V1 舊架構；新架構的數值核對層必須在 V10 execution 規格完成後另建，避免同一 V4 混入兩套策略。
- V10 SETUP gate 已收斂為 `Weekly bullish Bias + bullish Daily zone touch`；不新增 Daily MSS 前置 gate，也不恢復 Weekly OB/FVG。
- 所有 V10 後續 build 的右上版本識別表是驗收介面，不是一般顯示選項。任何 TradingView 截圖若未清楚顯示 build ID，不可用來做版本對答案或通過結論。
- 右上表未來加入統計時，BUILD 與 Weekly Bias 區塊固定保留在最上方；不得另外建立左側 Weekly Bias table，也不得讓統計欄位把版本或方向推離表格頂部。

## 目標

本專案把高週期 SMC 區域與 Daily 結構事件呈現在 TradingView Daily、H4、M15 等圖表，並優先維持 Bar Replay 的可見性與穩定性。

## 資料流程

1. 圖表 bars 聚合成完成的 Weekly candles，供 OB/FVG 使用。
2. Daily chart 直接用 chart candles；Intraday chart 聚合完成的 Daily candles。
3. OB/FVG 建立後保存在平行 arrays，逐 bar 檢查 midpoint invalidation。
   OB 僅在來源時框結構突破 candle body 通過 Wilder ATR(14) × 1.0 displacement 後建立；來源取 searchback 內最近反向 candle，範圍固定為保留遠端 wick 的 Hybrid Range。
   FVG 使用三根完成來源時框 candles 的標準 wick-to-wick gap，不設最小 gap 寬度；中間 candle 必須為同方向且 body 至少為 Wilder ATR(14) × 1.0。
4. CHOCH/MSS 各自維護 pivot、trend 與物件 arrays，事件成立時建立固定線段及透明文字 box。
5. 超過使用者設定上限時，從 arrays 前端刪除最舊物件。
6. SETUP 使用最新 Daily MSS bias 與 H1 對每個有效、尚未 traded 的 bullish Weekly zone 的獨立重疊狀態；只有 `zoneDir == 1` 且 bullish Bias 成立時，每個 zone 的 false → true 分別產生一次訊號。Bearish zone 與 bearish Daily structure 繼續維護及顯示，但不建立 execution flow。
   OB/FVG zone arrays 各自保存 `traded` 狀態，並與 zone 的建立及裁切保持相同索引生命週期。每個 zone 另以 flow 平行 arrays 保存 stage、SETUP/ARMED bar、break/retest/protect level。Re-entry 只替換同 zone 尚在 SETUP 的流程；已 ARMED 不受新 touch 影響。
7. 每個 ARMED 分別保存方向、來源 zone、起始 bar、突破位及保護位；同一次 H1 breakout 可讓多個 zone 各自 ARMED。Zone 失效會取消該 zone 尚未完成的候選；V1 每個確切 zone 只顯示最新 SETUP 標籤，ARMED 視覺物件仍依生命週期清理。
   ARMED 成立前以 active zone key 查找平行 SETUP label arrays，只暗化同 key 最新 SETUP，再清除候選狀態。
8. ENTRY 保存 ARMED 的方向、來源 zone、突破位、保護 swing 與起始 bar；首次有效回踩或取消後清除候選。Trade Plan 建立成功時立即將來源 zone 的 `traded` 設為 true，使同一確切 zone 永久停止建立新 SETUP；失敗流程不改變 `traded`。
   有效 ENTRY 後封存原 SETUP／ARMED 標籤並保留 ENTRY，形成固定歷史鏈；同 zone 後續 touch 不再具有刪除或取代該鏈的機會。
9. Trade Plan 使用平行 arrays 保存三條線、資訊 label、方向、四個價格、起始 bar 與狀態；狀態 0 為等待、1 為 TP1 且剩餘部位 SL 已移到 Entry、2 為 WIN、-1 為 Direct Loss 或 TP1→BE 結束。

## 統計資料流

- 開發與驗收的預設 TradingView 方案為 Essential，預設研究市場為台股；所有長期 Window 設計必須在此限制下成立。
- V3 是資料蒐集層，目標為無交易細節繪圖的 M30/H1/H4 三引擎統計；V1 是檢查層，保留單一時框的視覺交易鏈與逐筆 Trade Plan，兩者不得為了共用畫面而重新耦合。
- 現行研究 Window 強制固定為 1095D。V3 可使用 M30/H1/H4 chart；V4 PRIMARY 固定直接在 H1 chart 執行，與 V1 共用相同圖表資料邊界。完成標準必須包含實際覆蓋起點與 warm-up，不得只因表格顯示 1095D 就宣告完整。

- V1 僅維護一套 W-D-H1 狀態，正式入口固定為 H1 chart；H4/M30 與其他圖表不建立 SETUP/ARMED/ENTRY 候選。
- V4 PRIMARY 直接由 H1 chart bars 執行 W-D-H1，不再使用 H4 data carrier 或 H1 lower-timeframe arrays；另外兩列顯示為 LEGACY OFF，且不再執行。
- V1 與 V4 PRIMARY 是同一策略核心的兩種輸出：V1 用於圖形與逐筆檢查，V4 用於統計核對。Zone、Bias、Window、touch、flow stage、expiry、失效、交易結果與績效公式必須逐項相同；不得為了各自程式方便而改成不同判定。
- 正式台股 execution 固定 Long-only，方向過濾只放在 SETUP touch gate；下游 ARMED、ENTRY、Trade 與統計沿用同一 flow arrays，自然只包含多方，不在各階段重複維護另一套方向開關。
- 兩者的 1095D Window 都以第一根 Window H1 作為 touch-state 起點，不載入 Window 前的接觸狀態；第一根 H1 與有效 zone 重疊時，兩者都計入第一筆 Window touch。
- 開發順序固定為 V1 修改與 TradingView 驗證完成後，再移植相同核心到 V4；對齊時以共通統計欄位一致為完成條件。
- V1 在有效 Trade Plan 建立時累計 Total，交易結束時累計 TP2 win、TP1→BE、Direct Loss、Gross Win/Loss 與 Net R；圖形被 `Maximum trade plans` 裁切時，累計值不回退。
- 訊號漏斗另外記錄 SETUP、ARMED、Valid ENTRY、失效原因、SETUP/ARMED replacement、same/changed zone 與 OB/FVG 來源。
- V1 結果表的交易績效區與 `SIGNAL FUNNEL` 顯示分離；手機 compact 開關關閉 funnel 時只清除表格第 11～29 列。V4 橫向表格使用相同開關，在 compact 模式只重畫 MODEL、Total、TP2 Rate、Net R、Profit Factor 五欄；兩者底層計數器與策略流程均持續運作。
- V2 共用 Weekly zone 與 Daily bias，但 H4、H1、M30 各自保存 active SETUP、ARMED、pivot、交易 arrays 與績效累計，避免不同 Entry timeframe 互相清除狀態。
- V3 以 M30 作為唯一基礎資料流，但保留 M30、H1、H4 三套獨立狀態；每根完成 M30 更新 M30 引擎，每 2 根同一 H1 bucket 的 M30 聚合完成後更新 H1，每 8 根同一 H4 bucket 的 M30 聚合完成後更新 H4。
- V3 表格固定顯示 H4、H1、M30 三列；完整覆蓋顯示 `3TF V3`，`3TF PARTIAL` 代表 intrabar 歷史未覆蓋完整統計 Window。
- V2 的比較表位於右下角，使用 tiny 字體；V1 詳細表位於右上角，使用 tiny 字體。

## 關鍵設計決策

- Weekly OB/FVG 不依賴 `request.security()` 歷史繪圖，避免切換 timeframe 或 Replay 時物件不一致。
- V1 Weekly Zone 與 V4 各來源時框 Zone 共用同一 Wilder ATR、OB displacement／Hybrid Range 與 FVG geometry／middle displacement 公式；V1／V4 SWING 必須逐欄一致。
- V1 視覺層保留 bullish／bearish Weekly OB/FVG；Bullish 維持綠／黃，Bearish OB 與 FVG 使用兩階淺紅色。此配色只影響 box fill、border 與文字，不改 zone direction、建立、失效或交易判定。
- Intraday 的 Daily CHOCH/MSS 不採用已證實不穩定的 `request.security("D")` 顯示路徑，而由 intraday bars 重建完成日線。
- Intraday chart 只使用重建的 Daily CHOCH/MSS 狀態更新 Bias，不繪製 Daily 結構線與文字；結構物件只在 Daily chart 顯示。
- CHOCH 與 MSS 使用不同 pivot 長度：CHOCH 預設 2、MSS 預設 4。MSS 以較長 confirmed pivot、完成 Daily close 與 trend reversal 決定 Bias，不使用單根 ATR body displacement；兩者仍由 pivot scope 與用途保持區隔。
- Daily CHOCH/MSS 的固定執行順序為：完成 candle 先對先前 confirmed pivot 判斷 breakout、更新事件與 Bias，再發布本 candle 新確認的 pivot供後續 candle 使用；D chart 直接路徑與 H1 聚合 Daily 路徑必須一致。
- Daily MSS 預設 pivot length 為 4。MSS 建立 Bias 時固定保存當下反方向 confirmed Daily pivot；完成 Daily close 穿越該位置後 Bias 轉為 Neutral，且失效位不 trailing。
- 結構線是「pivot 到 breakout」的事實區段，而不是 future-facing ray。
- `line` 無文字能力，因此 MSS 使用透明 `box` 承載文字；CHOCH 的文字已隱藏，只保留結構線。
- SETUP/ARMED/ENTRY/Trade Plan 都是視覺分析層，不使用 `strategy.entry()`；Trade Plan 線與結果不代表實際成交。

## 非目標

- 目前不含交易下單、alert、正式策略回測或真實部位管理；Entry/SL/TP 只屬於 indicator 的視覺計畫與 OHLC 結果追蹤。
- 目前不含 365D High/Low。
- 目前沒有宣稱支援所有 symbol、session 或非標準 chart type。
