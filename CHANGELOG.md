# Changelog

## 2026-07-18 V10-BASELINE-01 research baseline freeze

- 只把indicator title與右上永久BUILD由`V10-DH1-ENTRY-05`升為`V10-BASELINE-01`；SETUP、ARMED、midpoint Buy Limit、Trade Plan、TP／SL、OB／FVG、1825D Window與全部統計行為均未修改。
- Baseline用於後續固定批次跨標的研究；每批只接受ETH H1、`1825D FULL`及相同Replay終點，不因已看過樣本的績效調整規則。
- 首批15檔為探索樣本，合計120筆有效交易、118筆已結束、2筆Open、Net R `35.5R`、平均`0.301R／已結束交易`；尚未計入手續費、交易稅與滑價。
- 6669／Daily實圖已確認右上`V10-BASELINE-01`、W BIAS、Daily zones與`SOURCE ETH`正常顯示；Daily的`USE H1`是預期支援提示，ETH H1 execution回歸仍保留。

## 2026-07-18 V10-DH1-ENTRY-05 fixed 1825D Window（Repository完成，待TradingView）

- V10 execution與全部漏斗／績效統計固定為`last_bar_time - 1825D`至`last_bar_time`；使用日曆日而非固定H1 bar數，讓相同Replay終點的不同標的使用相同日期範圍。
- Canonical Weekly／Daily zone engine與confirmed H1 pivot在Window前照常warm-up；per-zone SETUP／ARMED／ENTRY與Trade result loops只在Window內執行。Window前不建立execution state、不消耗`setupUsed`，第一根Window H1可對既有active zone建立第一筆Window SETUP。
- 右上表新增`WINDOW 1825D FULL/PART`與實際`FROM / TO`。最早可用ETH H1不晚於Window起點才是FULL；PART只供診斷，不得和FULL直接排名。Weekly request history由320提高到520週以保留5年Window前結構warm-up。
- Build升為`V10-DH1-ENTRY-05`；ENTRY-04持續SETUP tracking、midpoint Buy Limit、Trade Plan、OB／FVG來源統計與V1／V4均未修改。Repository靜態檢查完成後仍須TradingView驗證。

## 2026-07-18 V10-DH1-ENTRY-04 continuous SETUP tracking（2360指定路徑視覺通過）

- 修正2360／ETH H1的ARM前lifecycle：First-touch SETUP成立後，不再因H1 close離開Daily zone凍結tracking；離開、等待在zone外或re-entry期間，任何後續H1 lower low都持續移動同一SETUP marker並重新快照break level。
- 移除tracking凍結後`close < frozen SETUP low`取消candidate的路徑。ARM前waiting candidate現在只由Weekly Bias不再Bullish或Daily zone inactive／移除取消；break diagnostic line持續到其中一項hard invalidation或ARM transition。
- First-touch `setupUsed`、同zone單一SETUP、ARM close crossover、midpoint pending Buy Limit、Trade Plan、OB／FVG來源統計與V1／V4均未修改。Build升為`V10-DH1-ENTRY-04`。
- 2360／ETH H1 Replay確認marker隨後續lower low移動、zone exit／re-entry不重建SETUP、break line延伸至`B ARMED D OB`，且OB／FVG來源加總仍與全域閉合。Weekly／zone hard invalidation、same-bar與reload／Replay仍待補驗。

## 2026-07-18 V10-DH1-ENTRY-03 OB/FVG source statistics（首輪TradingView實圖通過）

- 右上永久表擴為label／OB／FVG三欄；頂部全域資訊維持合併顯示，來源區新增SETUP、ARMED、ENTRY、TP1 HIT、TP2、TP1→BE、DIRECT SL與NET R。
- 各階段在exact zone transition依`zoneType`累計；Trade Plan從保存的source type回寫TP／SL與R。TP1 HIT包含後續TP2／BE；same-bar SL+TP只算SL。
- Build升為`V10-DH1-ENTRY-03`。ENTRY-02 midpoint Buy Limit、Weekly-only撤單、2-tick SL、TP／BE／same-bar結果與V1／V4均未修改。
- 2317／ETH H1來源加總為SETUP `13+31=44`、ARMED `1+13=14`、ENTRY `1+10=11`、TP2 `1+5=6`、DIRECT SL `0+5=5`、NET R `1.5R+2.5R=4R`；2105亦以NET R `-1R+-1.5R=-2.5R`等全部欄位閉合。兩圖確認compile／runtime與來源表首輪通過。
- Weekly Bias撤單、same-bar衝突與reload／Replay仍是補充回歸，不因首輪通過而移除。

## 2026-07-18 V10-DH1-ENTRY-02 midpoint Buy Limit correction（待 TradingView）

- 修正ENTRY-01需求誤解：不在ARM下一根open直接成交。ARM成立時凍結該ARM bar high與final SETUP low，固定Buy Limit為兩者midpoint並對齊mintick；從下一根ETH H1起等待`low <= limit`才成交。
- Pending limit無時間expiry，只由Weekly Bias不再Bullish撤單。Daily zone inactive、frozen-low break與zone exit均不撤單；zone trimming跳過尚未成交的ARMED。新增淡青色pending limit line，成交或撤單時停止延伸。
- ENTRY成交後原2-tick SL、1R／2R、TP1→BE、成交bar`SL → TP2 → TP1`及紫紅same-bar conflict規則不變。Build升為`V10-DH1-ENTRY-02`；V1／V4未修改。

## 2026-07-18 V10-DH1-ENTRY-01 next-H1-open ENTRY and bracket plan（compile/runtime通過，規格淘汰）

- 依當時的需求誤解，取消frozen break retest／reclaim與midpoint掛單；每個ARMED在下一根completed ETH H1以該根open直接建立一次ENTRY。此行為後續確認並非使用者要的ENTRY。
- SL固定為ARM凍結的final SETUP low下方2 ticks；TP1=1R、TP2=2R、TP1出場50%後剩餘SL移到Entry。ENTRY bar立即判定結果並維持`SL → TP2 → TP1`保守順序。
- ENTRY bar若同時包含SL與TP，記為Direct Loss並將結果marker改為紫紅色`SAME BAR SL + TP`；一般Loss、TP1、TP2分別使用紅、青綠、藍。
- 新增獨立Trade Plan arrays、最多20組顯示／追蹤、ENTRY與簡要績效表；build ID升為`V10-DH1-ENTRY-01`。V1／V4與Daily zone定義未修改。
- 2317／ETH H1實圖顯示SETUP 44、ARMED 14、ENTRY／VALID 14、TP2 6、TP1→BE 3、Loss 5、Net R 5.5R，證明compile／runtime與next-open流程可執行；但使用者澄清ENTRY應為midpoint掛單，因此本build規格淘汰，由ENTRY-02取代。

## 2026-07-18 V10-DH1-ARMED-03 break level diagnostic（ARM 階段收尾）

- 新增預設關閉的 `Show ARM break level`；每個 waiting candidate以淡青色水平 dotted line顯示目前保存的 H1 break level。
- SETUP 建立或 tracking 中 lower-low refresh時，診斷線從該 snapshot bar重新起算並延伸；ARM transition、Weekly Bias／zone hard invalidation或 frozen-low invalidation時停止延伸並保留歷史。
- Break line handle加入 zone平行 arrays的 push／remove／trim lifecycle；break 為 `na` 時不建立。顯示開關不參與 pivot、snapshot、candidate、ARMED 或計數。
- Indicator title與永久表 BUILD 升為 `V10-DH1-ARMED-03`。ARMED-02 的全部行為、ENTRY／Trade／績效邊界、Daily zones及 V1／V4均未修改。
- 2317／ETH H1 實圖確認 compile／runtime、SESSION ETH、SETUP `44`、ARMED `14`及診斷線在 ARM transition停止；ARM 階段以此 build收尾。2634 exact no-ARM原因、取消路徑逐筆證據與 reload／Replay保留為後續回歸。

## 2026-07-18 V10-DH1-ARMED-02 persistent ARM candidate（第一輪多標的視覺通過）

- 每個 First-touch SETUP 建立時同步建立獨立 `waitingForArmed`；ARM transition 不再要求 lower-low tracking 仍為 true。
- Completed H1 close 離開 Daily zone只停止 tracking並凍結 SETUP low／break level，waiting candidate 繼續逐根檢查 close crossover；re-entry 不恢復 tracking，也不建立第二個 candidate。
- Waiting candidate 只由 Weekly Bias 不再 Bullish、Daily zone inactive／移除，或 tracking 凍結後 completed H1 close 跌破 frozen SETUP low取消；不增加時間 expiry。
- 右上 SETUP ACTIVE 改為 waiting candidate 數。Indicator title與永久表 BUILD 升為 `V10-DH1-ARMED-02`；ENTRY、Trade Plan、績效、Daily zone 定義、V1／V4均未修改。

## 2026-07-18 V10-DH1-ARMED-01 simplified per-zone ARMED（實圖 lifecycle 失敗）

- 新增 `Show H1 ARMED` 與預設 length 3 的 confirmed H1 swing high；SETUP 建立時保存 break level，tracking 期間只在 SETUP low 再創低時重新快照，不逐 pivot 追高。
- 每個 Daily zone 新增 setup bar、break level、ARMED marker、armed／active flag、ARMED bar與 frozen SETUP low，並與既有 zone arrays 保持相同 push／remove／trim lifecycle。
- Completed H1 先更新 lower-low／break snapshot並判斷 close crossover，再處理 close 離開 zone；ARMED 成立時停止 tracking、暗化 SETUP marker並顯示一次 `B ARMED`。
- ARMED ACTIVE 由 Weekly Bias 不再 Bullish、Daily zone inactive／移除或 completed H1 close 跌破 frozen SETUP low 結束；本 build 不加時間 expiry、ENTRY、Trade Plan、績效或 zone 定義修改。
- 右上永久表新增 ARMED TOTAL／ACTIVE，indicator title、BUILD 與 PHASE 升為 `V10-DH1-ARMED-01`。V1／V4 未修改。
- 2324／ETH H1 實圖確認 compile／runtime，但 Bullish Daily OB SETUP 離開 zone後的上漲未形成 ARMED，右上為 ARMED `0 / 0 ACTIVE`。原因是 zone exit 過早清除唯一 tracking／candidate state；此版由 ARMED-02 取代。

## 2026-07-18 V10-DH1-SETUP-02R1 canonical BOS display dedup（TradingView 通過）

- 2324／ETH H1 實圖發現約 30.5 出現兩條近乎平行、指向同一 BOS 區段的紅色 structure lines。
- BOS display 新增獨立 `direction + canonical BOS time` event key；`addDailyObBosLine()` 在建立 line／label 前先去重，同一事件最多一組 objects。
- BOS line、label、key 改為同一組 push／shift 生命週期。Broken-pivot time／price 仍只決定線段座標，不參與 event identity。
- SETUP-02 First-touch、Daily OB/FVG event與 geometry、Weekly Bias、canonical ETH feeds、V1／V4 均未修改。Build ID 升為 `V10-DH1-SETUP-02R1`。
- 使用者在 2324／ETH H1 確認 R1 可執行，原約 30.5 的同-event BOS 平行紅線已收斂為一條；右上維持 SETUP `24 / 0 ACTIVE`。SETUP 階段以此 build 收尾，下一步為 ARMED。

## 2026-07-18 V10-DH1-SETUP-02 First-touch only（由 R1 完成顯示修正）

- 每個 exact Daily OB/FVG 新增永久 `setupUsed` state；第一次有效 SETUP 建立時立即設為 true。
- SETUP-01 的 lower-low tracking、H1 close 離開停止、Weekly Bias／zone 失效停止均保留；離開後重新進入同一 zone 不再建立或累計第二個 SETUP。
- `SETUP TOTAL` 改為實際使用過的 unique exact zones 數；只有新生成、具有新 identity 的 Daily zone 可取得新 SETUP 機會。
- V1／V4、Daily zone 定義、Weekly Bias、canonical ETH feeds、ARMED／ENTRY 未修改。Build ID 升為 `V10-DH1-SETUP-02`。

## 2026-07-18 V10-DH1-SETUP-01 lower-low tracking SETUP（實圖可執行，規則被取代）

- V10 SETUP gate 收斂為 `Weekly Bullish Bias + active Bullish Daily OB/FVG H1 overlap`；取消原規劃的 Daily MSS 前置 gate，也不加入 SETUP expiry。
- ETH H1 首次進入每個 exact bullish zone 時建立一個 SETUP episode；同一段停留期間若 H1 再創新低，只移動同一 `B SETUP` marker，不增加 episode count。
- Tracking 在 Weekly Bias 不再 Bullish、完成 H1 close 離開 zone 上下界或 zone inactive／被移除時停止。離開後重新進入可建立新 episode；未來 ARMED 將沿用相同 tracking state 凍結 marker，本版尚未加入 ARMED。
- Daily zone arrays 新增 stable key、SETUP label、tracking、tracked low 與 previous-overlap state；顯示開關只控制 marker。右上永久表新增 SETUP TOTAL／ACTIVE，build ID 升為 `V10-DH1-SETUP-01`。
- V1／V4、Daily OB/FVG 定義、invalidation、Weekly Bias、canonical ETH requests 與 FVG-03 minimum-gap gate 均未修改。

## 2026-07-18 V10-FVG-03 isolated 0.50 ATR minimum gap（2105 Daily 視覺通過）

- 根據 FVG-02／2105 Daily 實圖，下方微小 FVG 已消失但約 59.5 的上方標記 FVG仍存在，因此固定 Daily FVG gap ATR multiplier 由 0.10 提高為 0.50；two-tick floor 保留。
- 本版只測試獨立 `max(K2 ATR × 0.50, 2 ticks)` gate，不加入歷史過嚴版本的 K3 close 順向半部條件。
- FVG-01 時間修正、midpoint invalidation、ETH feed、OB、Weekly Bias 與 no-execution state 均不修改。Build ID 升為 `V10-FVG-03`。
- 2105／Daily／SOURCE ETH 實圖確認上下兩個指定微小 FVG均已消失、其他主要 FVG仍可見，使用者判定結果合理；0.50 ATR gate 作為目前 V10 FVG 定義保留。跨時框 exact audit 與 Replay 仍待後續驗證。

## 2026-07-18 V10-FVG-02 minimum Daily FVG gap（2105 Daily 部分驗證）

- Daily FVG 新增固定最小 gap：`max(K2 Daily ATR × 0.10, syminfo.mintick × 2)`；Bullish／Bearish 分別以 `K3 low - K1 high`／`K1 low - K3 high` 計算 gap width。
- 不提供個別股票調參 input；目標只排除相對波動與最小跳動皆過小的微型 FVG，避免重複先前 `0.5 ATR + K3 half-range` 組合過嚴的問題。
- FVG-01 的 K2 body/ATR alignment、K1/K2/K3 metadata、canonical close-time endpoint、midpoint invalidation、ETH feed、OB、Weekly Bias 與 no-execution state 均不修改。Build ID 升為 `V10-FVG-02`。

## 2026-07-18 V10-FVG-01 K2 ATR alignment + canonical close-time endpoint（待 TradingView 驗證）

- Daily FVG 的中間 K body 改為比較中間 K 自身的 Daily Wilder ATR(14)，不再使用確認 K 的 ATR；標準三 K wick-to-wick geometry、無最小 gap 寬度、方向條件與 midpoint invalidation 均不變。
- Canonical Daily FVG 新增 K1 first time 與 K2 displacement/source time；K3 confirmation/event time 沿用 canonical Daily time，box 左端仍從 K3 開始。
- Daily zone state 新增 event time 與 FVG first time arrays，與既有 box、midline、type、direction、midpoint、active、source time、top、bottom 維持相同 push/remove/trim 生命週期。
- Canonical Daily snapshot 新增 completed-Daily `time_close`；OB/FVG 失效 box 與 midline 的右端由 chart-local `time` 改為 canonical close time，統一 Daily／H4／H1 endpoint。
- DZONE-09 ETH feed、DZONE-08 OB source、DZONE-07 BOS line、OB/FVG 上下界、FVG midpoint invalidation、Weekly Bias 與 no-execution state 保持不變。Build ID 升為 `V10-FVG-01`。

## 2026-07-18 V10-DZONE-09 canonical ETH session（待 TradingView 驗證）

- 新增 `canonicalEthTickerId = ticker.modify(syminfo.tickerid, session.extended)`，Weekly Bias 與 Daily OB/FVG canonical requests 統一使用 ETH，不再隨圖表 RTH／ETH 改變 request session。
- 右上永久表新增 SESSION 列：intraday ETH 顯示 `ETH`，其他 session 顯示 `USE ETH (...)`，Daily 以上顯示 `SOURCE ETH`。
- Pine 無法切換 TradingView 原生 chart bars；跨時框 Replay 與未來 H1 execution 驗收一律要求圖表設為 ETH。
- DZONE-08 的 OB source、BOS line、OB/FVG 上下界、失效、Weekly Bias 規則及 no-execution 保持不變。Build ID 升為 `V10-DZONE-09`。

## 2026-07-18 V10-DZONE-08 pivot-to-BOS extreme opposing source（待 TradingView 驗證）

- 移除 V10 Daily OB 固定 8 根 searchback 與「離 BOS 最近反向 K」來源規則。
- Bullish OB 改為：在被突破 pivot K 之後、BOS K 之前的所有 bearish K 中，選擇 `low` 最低者。Bearish OB 對稱選擇所有 bullish K 中 `high` 最高者。
- 左右端點排除、Doji 排除；同低／同高取時間較晚、較靠近 BOS 的反向 K。區間無反向 K 時不建立 OB。
- Engine 以 pivot 發布時初始化、後續逐 Daily candle 更新極值候選，避免動態長距離回掃；BOS candle 不進入候選。
- DZONE-07 的 broken-pivot-to-BOS line 三個座標、OB Full Range、ATR/BOS gate、失效、FVG、canonical feeds 與 no-execution 均未修改。Build ID 升為 `V10-DZONE-08`。

## 2026-07-18 V10-DZONE-07 OB BOS structure line（Daily 視覺通過）

- 依使用者澄清，移除 DZONE-06「BOS K → OB source K」斜向箭頭，改為「被突破 confirmed pivot K → BOS K」水平結構線。
- Canonical Daily OB event 新增 broken-pivot time／price；Bullish 使用被突破 swing high，Bearish 使用被突破 swing low。線條預設紅色、寬度 2，BOS K 保留方向 label。
- Input 改為 `Show Daily OB BOS structure line`，另提供 line color；line／label 各限制 40 個。
- OB 來源 K、searchback 8、Full Range、ATR/BOS gate、失效、FVG、Weekly Bias 與 no-execution 均未修改。Build ID 升為 `V10-DZONE-07`，待 TradingView 驗證。
- 使用者後續提供 2324／2634 Daily 實圖並確認水平 BOS line 符合需求；此顯示由 DZONE-08 原樣保留。

## 2026-07-18 V10-DZONE-06 OB BOS-to-source trace（需求不符合）

- 新增預設開啟的 `Show Daily OB BOS-to-source trace`，只在 OB 實際建立時產生診斷線與 BOS label。
- Bullish trace 從 BOS K low 連回來源 bearish K high；Bearish trace 從 BOS K high 連回來源 bullish K low，使用向左箭頭表達由 BOS K 往前搜尋並在來源 K 停止。
- Canonical Daily snapshot 新增 completed Daily high／low，連同既有 BOS time 與 source time 供 Daily/H4/H1 共用相同 trace 座標；trace lines／labels 各自限制 40 個。
- Daily OB/FVG 的生成、上下界、失效、Weekly Bias 與 no-execution state 未修改。使用者實圖確認可執行，但此連線端點源於需求誤解，已由 `V10-DZONE-07` 取代。

## 2026-07-18 V10-DZONE-05 canonical completed-Weekly Bias（跨時框表格通過）

- 根據 2324 `V10-DZONE-04` 的 Daily Bearish `20/20`、H4 Bullish `16/15`、H1 Bullish `10/10`，移除由各 chart bars 自行聚合 Weekly OHLC、pivot、Bias 與 flips 的路徑。
- 新增 `canonicalWeeklyBiasEngine()` 在 Weekly request context 執行 prior-pivot breakout ordering、Bias 更新、Bull/Bear flip counts 與新 pivot 發布；`confirmedWeeklyBiasSnapshot()` 對 8 個 outputs 全部使用 `[1]` 並搭配 `lookahead_on`。
- W BULL／W BEAR markers 只在新的 canonical Weekly snapshot 到達時顯示一次；背景、swing steplines 與右上 Weekly 區塊共用相同 canonical state。
- `V10-DZONE-04` canonical Daily Zone Engine、OB/FVG 定義、失效、objects、顏色、上限及 no-execution state 未修改。
- Build ID 升為 `V10-DZONE-05`；Repository 靜態 assertions、Markdown 相對連結及 `git diff --check` 通過。
- 2324 同一 Replay 位置的 Weekly／Daily／H4／H1 實圖均顯示 Bullish、swing high `47.75`、swing low `27.50`、flips `8 / 7`；canonical Weekly table 跨時框對齊通過，Daily zones 第一輪視覺無 regression。Marker one-shot、reload／Replay 與 exact-zone 數值仍待驗證。

## 2026-07-17 V10-DZONE-04 canonical Daily OB/FVG feed（第一輪跨時框視覺通過）

- 根據 2324 Daily 有 Bullish OB、H1 缺少相同 OB 的實圖失敗，移除由 chart bars 各自重建 Daily OHLC、Wilder ATR、pivot、BOS 與 FVG 的路徑。
- 新增單一 `canonicalDailyZoneEngine()`，在 Daily request context 計算 ATR、confirmed pivot、一次性 BOS、OB source／Full Range 與 FVG event；`confirmedDailyZoneSnapshot()` 對全部輸出使用一根歷史位移並搭配 `lookahead_on`，只發布已完成 Daily snapshot。
- Daily 與 H1 chart 只在相同 canonical Daily time 到達時處理一次失效與 zone 建立；box／line 及九組 zone 平行 arrays 仍留在 chart context。
- OB/FVG 定義、OB full-edge invalidation、FVG midpoint invalidation、顏色、上限、Weekly Bias 與 no-execution state 未修改。
- Build ID 升為 `V10-DZONE-04`；Repository 靜態 assertions 與 `git diff --check` 通過。2324 Daily/H4/H1 compile/runtime 與第一輪 zone 位置視覺一致，先前 H1 缺失的兩個 Bullish OB 已恢復；精確數值、逐區失效日與 Replay/reload 仍待核對。

## 2026-07-17 V10-DZONE-03 traditional Daily OB candidate（跨時框驗證失敗）

- Daily OB 結構由 rolling lookback high／low 改為獨立 confirmed Daily pivot，預設 swing length 4；每個 pivot 只接受一次首次 close BOS，完成 Daily candle 固定先判斷並消耗舊 pivot，再發布本 candle 新確認的 pivot。
- Bullish／Bearish OB 分別要求 bullish／bearish 突破 K，body 至少為 Daily Wilder ATR(14) × 1.0；來源限制在被突破 pivot 之後，取 searchback 內最近反向非 Doji Daily candle。
- V10 Daily OB range 由 Hybrid Range 改為來源 K Full Range `low → high`；完成 Daily close 嚴格穿越遠端 bottom／top 才失效。
- Daily FVG 的三 K geometry、ATR displacement、range 與 midpoint invalidation 未修改；Weekly Bias、永久右上表、zone arrays、顏色、上限及 no-execution state 保留。
- Build ID 升為 `V10-DZONE-03`；Repository 靜態 assertions、Markdown 相對連結及 `git diff --check` 通過，仍須由使用者執行 TradingView compile/runtime 與 Daily／H1 實圖驗證，不得沿用 `V10-DZONE-02` 的通過結論。
- 2324 實圖顯示 Daily 有兩個 Bullish OB，切換 H1 後 OB 消失而 FVG 保留；Daily/H1 Weekly flips 亦為 `20/20` 對 `10/10`，確認 chart-driven 高週期 state 受歷史起點影響。本 build 不通過，OB 定義保留並由 `V10-DZONE-04` 修正資料源。

## 2026-07-16 V10-DZONE-02 integrate Weekly Bias into right table（視覺通過）

- 移除左側獨立 Weekly Bias table，Weekly Bias、confirmed swing high／low與 flip 次數整合到右上永久表。
- 右上固定順序為 BUILD → W BIAS → W SWING HIGH → W SWING LOW → W FLIPS → PHASE → D ZONES。
- W BIAS value 保留 Bullish／Bearish／Neutral 顏色；未來績效統計只能接在此固定區塊下方。
- Daily OB/FVG 建立、失效、顏色、objects、Weekly Bias 核心與所有顯示開關均未修改。
- Repository 靜態檢查確認只剩一個 `position.top_right` table、固定 row ordering、無左側重複表、Daily engine 保留且仍無 execution；Markdown 證據連結與 `git diff --check` 通過。
- 2105／Weekly、Daily、H1 實圖確認永久表可見、左側表已移除、目前 Weekly Bias 與 confirmed levels 一致，Daily/H1 zones 第一輪顯示正常。

## 2026-07-16 V10-DZONE-01 Daily OB/FVG and permanent build table（第一輪視覺通過）

- 在 `V10-WBIAS-04` clean baseline 上新增完成 Daily candle 驅動的 OB/FVG，不恢復 Weekly zones。
- Daily OB 使用結構突破、Daily Wilder ATR(14) × 1.0 displacement、最近反向 candle 與 Hybrid Range。
- Daily FVG 使用標準三根完成日 K wick-to-wick gap；中間 K 同方向且 body 至少為 Daily ATR × 1.0。
- Daily zones 由完成 Daily close 穿越 midpoint 失效；midline 預設關閉，每類最多 40 個。
- 新增不可隱藏的右上永久版本識別表，固定顯示 build ID、phase 與 Daily zone 支援狀態。
- 本版只建立與顯示 Daily zones，不含 Daily MSS、SETUP、ARMED、ENTRY、Trade Plan 或績效。
- Repository 靜態檢查確認 Weekly/Daily 聚合、ATR、OB/FVG、Daily midpoint invalidation、永久 build table 與九組 zone 平行 arrays push/remove 一致；2376／Weekly、Daily、H1 compile/runtime 與第一輪 zone 顯示通過。

## 2026-07-16 V10-WBIAS-04 remove Weekly zones and legacy execution（視覺通過）

- 依使用者要求，在加入 Daily OB/FVG 前從 V10 真正刪除 Weekly OB/FVG，而非只關閉顯示。
- V10 已移除 Weekly zone inputs、ATR／OB／FVG geometry、arrays、boxes、midlines、midpoint invalidation、touch/traded state及相關 helper functions。
- 因舊 SETUP／ARMED／ENTRY 與交易統計依賴 Weekly zones，legacy execution、Trade Plan 與右上結果表亦一併移除。
- V10 現為精簡的 Weekly Structure Bias-only indicator；保留週方向表、紅綠背景、flip markers 與預設關閉的 swing levels。
- V1 `V1-LONG-01`、V4 `V4-LONG-01` 完全不修改，舊架構仍由兩者保存。
- Repository 靜態檢查確認 V10 已無 OB/FVG、zone objects 或 legacy execution，固定使用完成 Weekly candles；2376／Weekly 實圖確認 clean baseline 通過，但也暴露缺少 build table 的工作流程問題。

## 2026-07-16 V10-WBIAS-03 simplified default display（視覺通過）

- 使用者確認 `V10-WBIAS-02` 的週多／週空表格與紅綠背景清楚、簡單且符合需求。
- 長歷史 swing steplines 與 OB/FVG 重疊造成畫面混亂，因此 `Show Weekly Bias swing levels` 預設由開啟改為關閉；使用者仍可手動開啟核對。
- 紅綠 Weekly Bias background 維持預設開啟；Bias 核心、flip markers、legacy execution、V1 與 V4 均未修改。
- V4 不同步此 V10 中間階段；待 V10 Daily zones 與新 execution 完成後，另建同架構數值核對版本。
- Repository 靜態檢查、預設值 assertion、Markdown 圖片連結與 `git diff --check` 通過；2376／Weekly 實圖確認 swing levels 預設關閉、背景與 Bias 顯示正常。

## 2026-07-16 V10-WBIAS-02 visible Weekly direction（視覺通過）

- 使用者提供 2376/Weekly 與 H1 實圖，確認 `V10-WBIAS-01` compile/runtime 正常，兩時框的 confirmed swing high／low同為 402.0／318.5。
- 原 `position.top_left` 的目前 Bias 列被 TradingView 商品資訊遮住，無法直接辨識現在週多或週空；Weekly/H1 的 flip 次數亦因歷史覆蓋不同而不可作為目前方向。
- Build 升為 `V10-WBIAS-02`，方向表移至 `position.middle_left`，第一列放大顯示 `週多 BULLISH／週空 BEARISH／中性 NEUTRAL`。
- 新增 confirmed swing high／low steplines、`W BULL／W BEAR` flip markers 與可關閉的極淡方向背景。
- Weekly Bias 判定、swing length 2、prior-pivot ordering、Weekly OB/FVG、legacy execution 與 V1／V4 均未修改。
- Repository 靜態檢查、顯示路徑 assertion、Markdown 圖片連結與 `git diff --check` 通過；2376／Weekly 實圖確認方向與背景可見，後續只因畫面雜訊將 swing levels 預設關閉。

## 2026-07-16 V10-WBIAS-01 independent Weekly Structure Bias（compile/runtime 通過，方向顯示失敗）

- 新增獨立 `smc_weekly_structure_bias_v10.pine`，不修改 V1 `V1-LONG-01` 或 V4 `V4-LONG-01`。
- V10 使用完成 Weekly candles 與 confirmed pivot length 2 建立獨立 Weekly Structure Bias；完成 close 突破先前 confirmed swing high／low時更新 Bullish／Bearish。
- Weekly Bias 不讀取 OB/FVG zone、touch、active、traded 或 invalidation 狀態，也不使用 ATR displacement。
- 新增左上角 Bias 表格，顯示目前方向、confirmed swing high／low與 Bias flip 次數；非 W high-timeframe 顯示 `USE W SOURCE`。
- 本階段尚未把 Weekly Bias 接入 execution；複製自 V1 的交易表標示 `LEGACY EXEC`，避免誤認為新架構績效。
- Repository 靜態檢查與 `git diff --check` 通過；2376／Weekly、H1 compile/runtime 及 confirmed levels 一致，但左上方向列被商品資訊遮住，因此顯示驗收失敗並升版修正。

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
