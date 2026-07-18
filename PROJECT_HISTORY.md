# TradingView SMC Replay Toolkit - Development History

## 2026-07-18 - V10凍結為Baseline並完成首批15檔探索統計

使用者完成首批15檔ETH H1／1825D FULL截圖統計後，決定暫停修改策略條件，將ENTRY-05現行行為凍結為`V10-BASELINE-01`。本次升版只更改indicator title與右上BUILD識別；Weekly Bias、Daily OB/FVG、SETUP持續tracking、ARM、midpoint pending Buy Limit、TP1／TP2／SL及來源統計全部不變。目的不是宣告策略已可實盤，而是建立下一批樣本可重複比較的固定規則。

首批標的為2360、2454、3017、2615、2317、3443、2382、3034、2383、2368、8064、3231、6116、6515、6451。合計SETUP 393、ARMED 148、有效交易120、已結束118、Open 2、TP2 41、TP1→BE 34、Direct SL 43、Net R 35.5R，平均0.301R／已結束交易。OB為32筆已結束／4R／0.125R每筆；FVG為86筆已結束／31.5R／0.366R每筆。這些都是未扣手續費、交易稅與滑價的探索結果，不得據此回頭修改Baseline；OB／FVG差異只作下一批未見樣本的驗證假設。

ENTRY-05畫面已涵蓋15檔`WINDOW 1825D FULL`、ETH、FROM 2021-07-18、TO 2026-07-17及來源加總資料，但Window第一根H1、PART、Weekly取消、same-bar、reload／Replay等特殊路徑仍未逐項留下證據。Baseline是研究凍結點，不把上述未驗證項目改寫成已完成。

升版後使用者提供6669／Daily的`V10-BASELINE-01`實圖，永久表BUILD、Weekly Bias、Daily zones與`SOURCE ETH`正常，execution列依非H1支援邊界顯示`USE H1`及`1825D USE H1`。此圖完成Baseline版本識別與Daily runtime確認，不代替ETH H1 execution回歸。

## 2026-07-18 - V10跨標的比較固定為5年

使用者決定後續V10跨標的統計採固定5年。正式邊界定為1825個日曆日，結束點使用`last_bar_time`，不以5000或其他固定H1 bar數近似；相同Replay終點下各標的因此具有相同理論FROM／TO。

`V10-DH1-ENTRY-05`把Weekly Bias、Daily zones與confirmed H1 pivot留在Window前warm-up，但把SETUP、ARMED、ENTRY、Trade與全部漏斗／績效限制在Window內。第一根Window H1不承接Window前touch state，仍可使用當時已存在的active Daily zone建立第一個SETUP。表格以`FULL/PART`區分資料覆蓋，PART不得和FULL直接排名；V1／V4既有1095D基準不變。

## 2026-07-18 - 2360揭露SETUP tracking過早凍結

使用者在2360／ETH H1／ENTRY-03指出Bullish Daily OB已有SETUP，但淡藍break line只出現短段且沒有OB ARM；並確認線停止當下Weekly Bias仍為Bullish。畫面同時顯示SETUP marker沒有隨後續H1價格下降移動。

逐行檢查確認兩個現象來自同一路徑：H1 close離開zone時舊規格先把tracking設為false，之後lower low不再更新SETUP marker／break snapshot；completed H1 close再跌破這個舊SETUP low時，程式取消waiting candidate並停止break line。這不是單純繪圖錯誤，也不是Weekly Bias取消。

使用者確認新流程：First-touch SETUP成立後持續追蹤所有後續H1 lower low直到ARM，不因離開或重回zone停止；同zone仍只計一次SETUP。ARM前只由Weekly Bias轉向或Daily zone inactive取消。`V10-DH1-ENTRY-04`依此移除zone-exit freeze與frozen-low取消路徑；ENTRY-03的midpoint掛單、Trade Plan與來源統計不變。後續2360／ETH H1 Replay確認SETUP marker移到新低、break line延伸至`B ARMED D OB`且來源加總閉合，指定路徑視覺通過。

## 2026-07-18 - ENTRY-03首輪驗收與階段收尾

使用者提供2317與2105／ETH H1／`V10-DH1-ENTRY-03`實圖。兩檔均正常顯示BUILD與SESSION，來源表的SETUP、ARMED、ENTRY、TP1 HIT、TP2、TP1→BE、DIRECT SL及NET R都能由OB與FVG精確回加到全域統計。2317為`1.5R + 2.5R = 4R`，2105為`-1R + -1.5R = -2.5R`；2317另有ARMED 14、ENTRY 11、3 ACTIVE，支持ARM後以midpoint Buy Limit等待而非立即成交。

本階段凍結ENTRY核心，不再調整參數。兩張圖都沒有SAME BAR案例，且未以Replay逐步證明Weekly Bias改變撤單或reload穩定性，因此這三項保留為補充回歸；不把來源統計首輪通過擴大寫成所有特殊路徑已完成。

## 2026-07-18 - ENTRY-03新增OB／FVG來源統計

使用者要求分別觀察Daily OB與FVG的訊號漏斗及績效。ENTRY-03不改midpoint掛單或Trade Plan，只在per-zone SETUP／ARMED／ENTRY transition依zone type累計，並由Trade Plan保存的source type回寫TP1、TP2、TP1→BE、Direct SL與Net R。TP1 HIT包含最後走到TP2或BE的交易；same-bar SL+TP依保守順序只歸SL。右上表改為label／OB／FVG三欄，來源加總必須和全域總計對齊。

## 2026-07-18 - ENTRY-01 next-open需求誤解與ENTRY-02 midpoint修正

ENTRY-01依前一次文字理解為ARM後下一根ETH H1 open直接成交。使用者提供2317／ETH H1實圖，證明build可compile／runtime，右上為SETUP 44、ARMED 14、ENTRY／VALID 14、TP2 6、TP1→BE 3、Loss 5、Net R 5.5R；但14個ARM全部立即轉ENTRY也清楚暴露需求誤解。

正式定義改為：ARM completed H1凍結ARM high與final SETUP low，兩者midpoint是固定Buy Limit；ARM當根不回填，從下一根起無限等待觸價，只有Weekly Bias改變撤單。ENTRY-02新增per-zone ARM high、limit level與pending line，zone trimming不得刪除pending order；成交後沿用2-tick SL、1R／2R、TP1→BE及成交bar SL優先。ENTRY-01不得恢復。

## 2026-07-18 - ENTRY-01初版解讀（已由ENTRY-02取代）

本段當時把需求解讀為放棄break-level retest／reclaim與midpoint掛單，改成ARM下一根H1 open直接ENTRY。後續2317實圖回報後，使用者澄清真正需求始終是ARM high與final SETUP low midpoint掛單，因此本段只保留為需求誤解紀錄，不再代表現行規格。

ENTRY-01仍建立了可重用的Trade Plan基礎：SL為final SETUP low下方2 ticks，TP1=1R、TP2=2R，TP1出場50%後其餘SL移至Entry；同bar涵蓋SL與TP時保守記Direct Loss並用紫紅marker區別。ENTRY-02只修正ENTRY價格與pending lifecycle，保留這些結果規則。

## 2026-07-18 - V10 ARM 階段以 ARMED-03 收尾

ARM 最初採用簡化的 per-zone break：SETUP 建立時快照 confirmed H1 swing high，只有 SETUP low 再創低時更新。ARMED-01 把 ARM 資格綁在 lower-low tracking，導致價格先離開 Daily zone再突破結構時永遠不會 ARM；2324 實圖揭露此 lifecycle 假設錯誤。

ARMED-02 將 `waitingForArmed` 與 tracking 分離。SETUP 一成立即成為候選，zone exit只凍結 low／break；只有 Weekly Bias轉向、Daily zone inactive／移除或 completed H1 close跌破 frozen SETUP low取消。2324、2634、2317／ETH H1 第一輪 marker／count 視覺通過。

ARMED-03 只增加預設關閉的 break level dotted line，不改 state。使用者提供 2317／ETH H1 畫面，確認 build正常執行、ARMED仍為 `14`，診斷線由 snapshot延伸並在 ARM transition停止，因此同意 ARM 階段以此 build收尾。可重用原則是：SETUP 價格追蹤與 ARM 候選生命週期必須分離；不確定門檻原因時先加入 display-only diagnostic，再決定是否改規則。Reload／Replay、2634 exact no-ARM原因與三項取消逐筆證據保留為後續回歸。

## 2026-07-18 - ARMED-02 多標的初驗與 break level 診斷

使用者提供 2324、2634、2317／ETH H1／`V10-DH1-ARMED-02` 實圖。三檔均正常顯示 build、Bullish Weekly Bias、D ZONES ACTIVE、SESSION ETH及 SETUP／ARMED totals；ARM/SETUP 為 7/24、9/35、14/44，比例約 26%～32%。2324 與 2317 可見 ARM markers落在 SETUP 後的向上結構推進，使用者認為目前結果可以。

下一步不調整 ARM 核心，只增加預設關閉的 break level診斷。`V10-DH1-ARMED-03` 為每個 waiting candidate顯示其保存的 H1 break dotted line，snapshot更新時移線，ARM或取消時停止延伸。目標是先釐清 2634 可見 SETUP未 ARM的 exact threshold，再決定是否需要處理 pre-crossed break；本輪不提前修改條件。

## 2026-07-18 - ARM candidate 與 SETUP tracking 分離

使用者提供 2324／ETH H1／`V10-DH1-ARMED-01` 實圖：Bullish Daily OB 已建立 SETUP，後續價格離開 zone並明顯上漲，但右上 ARMED 仍為 `0 / 0 ACTIVE`。原因是 ARMED-01 只在 lower-low tracking 存活時檢查 break；價格第一次 close 離開 zone但尚未跨越較高的 H1 break level後，後續 bars 永遠失去 ARM 判斷資格。

使用者固定新定義：SETUP 一成立就是 ARM candidate，是否 ARMED 只由 ARM 邏輯判斷。`V10-DH1-ARMED-02` 新增獨立 waiting state；zone exit 只凍結 SETUP low／break並停止 tracking，不取消候選。Pre-ARM candidate 與 ARMED ACTIVE 都只由 Weekly Bias 轉向、Daily zone inactive／移除或 completed H1 close 跌破 frozen SETUP low取消，不加入時間 expiry或 re-entry reset。

## 2026-07-18 - V10 第一個簡化 ARMED 候選

使用者希望 ARMED 維持簡單並同意採用 per-zone 改良方案。`V10-DH1-ARMED-01` 不直接照搬 V1 的 SETUP-time fixed pivot：SETUP 建立時先快照最新 confirmed H1 swing high，之後只有 tracked SETUP low 再創低時才重新快照；其他新 pivot 不移動 break level。

Transition 只由仍 tracking 的 First-touch SETUP 發生。Completed H1 先處理 hard invalidation與 lower-low，再判斷 close crossover，最後才處理 close 離開 Daily zone，使向上突破 zone top 的同一根 K 仍有機會成立 ARMED。成立後凍結該 zone 的最終 SETUP low、break level 與 ARMED bar，停止 tracking並暗化原 marker；ACTIVE 由 Weekly Bias、zone state 或 close 跌破 frozen low 取消。第一版只加入 marker、per-zone state 與 TOTAL／ACTIVE，不含 ENTRY、Trade Plan、績效或時間 expiry。

## 2026-07-18 - V10 SETUP 階段收尾，下一步 ARMED

使用者提供 2324／ETH H1／`V10-DH1-SETUP-02R1` 收尾畫面。右上 BUILD、First-touch phase、SESSION ETH 與 SETUP `24 / 0 ACTIVE` 均可見；原先約 30.5 的同-event BOS 平行紅線已收斂為一條，確認 R1 compile／runtime 與 canonical BOS display dedup 通過，且顯示修正沒有改變此案例的 SETUP TOTAL。

使用者同意 SETUP 以 R1 收尾，下一個獨立階段改做 ARMED。ARMED coding 前必須先固定 break level 來源與 transition 規格；第一個 build 只處理 active First-touch SETUP → ARMED、凍結最終 SETUP low 與 per-zone marker／state，不提前加入 ENTRY、Trade Plan 或績效。First-touch re-entry、lower-low、reload／Replay 未由本張截圖逐根證明，保留為 ARMED 開發時的 SETUP regression。

## 2026-07-18 - V10 canonical BOS 顯示去重

使用者在 2324／ETH H1／`V10-DH1-SETUP-02` 發現約 30.5 有兩條近乎平行的紅色 BOS structure lines。現有 Daily OB source 具備去重，但 BOS line／label 本身沒有 canonical event key，因此顯示層缺少最後一道 one-event-one-object 保護。

`V10-DH1-SETUP-02R1` 以 `direction + canonical BOS time` 作為 display identity；相同 key 已存在時不再建立 line／label。Broken pivot仍決定 x1／price，BOS time 決定 x2，但座標差異不應讓同一 BOS event 重畫。First-touch SETUP、zones 與 Weekly Bias 不修改。

## 2026-07-18 - V10 SETUP 改為 First-touch only

使用者以 2105／ETH H1 檢視 `V10-DH1-SETUP-01`；右上顯示 SETUP `184 / 0 ACTIVE`，圖上已有 D FVG SETUP。使用者認為同一 OB/FVG 離開後再進入便重新 SETUP 不符合 SMC 使用直覺，決定先採更嚴格的 First-touch only。

`V10-DH1-SETUP-02` 為每個 exact Daily zone 新增永久 `setupUsed`。第一次有效 SETUP 立即 consumed SETUP 機會，但仍在同一次 tracking 期間移動 marker 到後續 H1 lower low；離開、Weekly Bias 轉向或 zone 失效後停止 tracking，re-entry 永遠不建立第二個 SETUP。只有新 identity 的 Daily OB/FVG 才能產生新 SETUP。

## 2026-07-18 - V10 SETUP 簡化為 Weekly Bias + Daily zone lower-low tracking

使用者決定不加入原規劃的 Daily MSS SETUP gate。V10 SETUP 改為只有 Weekly Structure Bias 為 Bullish 時，ETH H1 進入 active Bullish Daily OB/FVG 即建立；同一段停留期間不重複新增 SETUP，而是每當 H1 low 再創低就移動同一 marker。Tracking 持續到未來 ARMED、H1 close 離開 zone、Weekly Bias 不再 Bullish 或 zone 失效／移除。

`V10-DH1-SETUP-01` 保留 FVG-03 與既有 canonical zone／Weekly engines，只增加 exact-zone SETUP state 與右上 TOTAL／ACTIVE。ARMED 尚未實作，因此本版只驗證 SETUP marker 與 lifecycle；V1／V4 不修改。

## 2026-07-18 - V10 獨立測試 0.50 ATR gap

使用者提供 `V10-FVG-02`／2105 Daily／SOURCE ETH 實圖：原最下方 OB 上的微小 FVG已消失，但約 59.5、由兩條黃虛線標出的上方 FVG仍存在。這證明 0.10 ATR gate 可排除較小 gap，但未達到本輪全部視覺目標。

使用者決定把 gap ATR multiplier 提高為 0.50。`V10-FVG-03` 只修改此常數並保留 two-tick floor，不恢復 2026-07-13 歷史版本的 K3 close 順向半部條件，因此這次可單獨觀察 0.50 ATR gap width 的影響。

使用者後續提供 `V10-FVG-03`／2105 Daily／SOURCE ETH 實圖。FVG-02 已移除的下方微小 FVG持續消失，約 59.5 的上方標記 FVG亦已排除，其他主要 FVG仍可見；使用者判定結果合理。FVG 階段因此以 `max(K2 ATR × 0.50, 2 ticks)` 作為目前保留定義收尾。這只完成 2105 Daily 視覺驗證，跨時框 exact values、endpoint、reload 與 Replay 未被截圖證明，仍保存為後續限制。

## 2026-07-18 - V10 微小 FVG 過濾候選

使用者在 2105／Daily 指出最下方 Bullish OB 上方有一個視覺上過小的 FVG，決定先試用固定 `max(K2 ATR × 0.10, 2 ticks)` minimum gap。這個門檻同時對齊波動與最小價格跳動，不依 OB overlap 改變 FVG 定義，也不提供個別股票參數。

Repository 曾在 2026-07-13 測試 `0.5 ATR gap + K3 range half` 組合並因整體過嚴而撤回；該歷史不能視為 `0.10 ATR` 的獨立驗證。`V10-FVG-02` 因此只作為新候選，必須先確認指定微小 FVG 消失且主要 FVG 未大量減少，再決定是否保留。

## 2026-07-18 - V10 FVG 時間語意與跨時框 endpoint

使用者同意兩項 FVG 修正：中間 displacement K 的 body 必須比較同一根 K 自身的 Daily ATR，而不是確認 K ATR；Daily／H4／H1 的 zone 失效右端必須使用 canonical completed-Daily `time_close`，不能使用 chart-local `time`。使用者不同意把 midpoint 改成 mitigation lifecycle，因此既有完成 Daily close 穿越 midpoint 失效規則完全保留。

`V10-FVG-01` 在 DZONE-09 基礎上分離 FVG 的 K1 first、K2 displacement/source 與 K3 confirmation/event time；box 仍由 K3 開始。Zone state 增加 event／first time metadata，canonical snapshot 增加 Daily close time。這些修改只處理 FVG 時間一致性與跨時框繪圖終點，不改 OB source、BOS line、Weekly Bias、ETH session 或 execution boundary。

## 2026-07-18 - V10 統一 ETH session

2105／2023-12-21 出現 Daily canonical 結構價 47.90、H1 RTH 無任何 K 棒觸及的疑問。使用者移除指標後比對 Daily 與 H1，確認 H1 切到 ETH 即出現觸及 47.90 的 K；正新該年度除息日在 2023-06-01，因此排除除權息。決策是 V10 的 Weekly／Daily canonical requests、Replay 驗收與未來 H1 execution 全部統一 ETH。`V10-DZONE-09` 以 session-specific ticker 固定內部 request，並在右上表警告非 ETH intraday chart；Pine 不能切換原生圖表 session，這項使用者設定仍是驗收前置條件。收尾截圖仍顯示 DZONE-08，故只證明 ETH 原生 H1 的 47.90，DZONE-09 compile／SESSION row 仍保留為下一輪第一個安全測試點。

## 2026-07-18 - V10 OB source 改為 pivot-to-BOS 極值反向 K

使用者以 2324、2634／Daily 實圖確認 `V10-DZONE-07` 的 broken-pivot-to-BOS 水平線符合需求，並要求三個 BOS line 座標保持不變，只修改 OB source。`V10-DZONE-08` 將來源範圍固定為 pivot K 與 BOS K 之間：Bullish 取 `low` 最低的 bearish K，Bearish 取 `high` 最高的 bullish K；端點與 Doji 排除，同價取較靠近 BOS 者，區間無反向 K 則不建立 OB。原固定 8 根與最近反向 K 規則從 V10 移除。

## 2026-07-18 - V10 BOS structure line correction

`V10-DZONE-06` 將 BOS K 斜向連到 OB source K；使用者實圖確認功能可執行，但澄清真正需求是將「突破形成 BOS 的 K」水平回畫到「被突破的 confirmed pivot K」，價位固定為該 swing high／low，類似手動畫出的紅色結構線。`V10-DZONE-07` 因此新增 broken-pivot time／price outputs 並取代 source trace；OB source 搜尋與全部成立規則保持不變。

## 2026-07-18 - V10 OB BOS-to-source visual audit

使用者最初要求增加 OB 診斷線；當時理解為顯示「由 BOS K 往前最多 8 根尋找最近反向 K」的結果，因此 `V10-DZONE-06` 將 BOS K 連到來源反向 K。後續實圖澄清此理解錯誤，本版不再使用。

## 2026-07-17 - V10 canonical completed-Weekly Bias

`V10-DZONE-04` 已修正 Daily zones，但 2324 同一 Replay 位置的 Weekly table 仍為 Daily Bearish／`20/20`、H4 Bullish／`16/15`、H1 Bullish／`10/10`。這證明原 chart-driven Weekly 聚合與 Daily zones 先前問題相同：各時框載入歷史起點不同，造成 Bias 路徑與累計 flips 不一致。後續 H1 SETUP 需要可靠 Weekly 方向，因此不能只修 zones。

`V10-DZONE-05` 將 Weekly pivot、prior-pivot break、Bias、flip counts 與 markers 移入單一 Weekly `request.security()` context。所有 outputs 位移一根 Weekly bar並搭配 `lookahead_on`，chart 端只在 canonical Weekly time 改變時發布一次 marker；table、背景與 levels 直接讀取同一 state。Daily canonical engine 完全保留；本版完成時的下一步是等待 2324 Daily/H4/H1 四個 Weekly table 欄位精確一致驗證。

使用者後續提供 2324 同一 Replay 位置的 Weekly、Daily、H4、H1 四張 `V10-DZONE-05` 實圖。四個時框的永久表均為 `週多 BULLISH`、confirmed swing high `47.75`、swing low `27.50`、Bull/Bear flips `8 / 7`；Weekly chart 正確標示 Daily zones 僅供 Daily／intraday 使用，Daily、H4、H1 的既有 zones 仍可見。Canonical Weekly table reconciliation 因此通過；marker one-shot、切換／reload、Replay 與 Daily zone exact values 仍保留為下一輪 audit，不能由靜態截圖推定完成。

## 2026-07-17 - V10 canonical Daily Zone Engine

`V10-DZONE-03` 在 2324 實圖出現 Daily 有兩個 Bullish OB、H1 只剩 FVG的差異。畫面同時顯示 Daily/H1 Weekly flip counts 為 `20/20` 與 `10/10`，證明原本依各 chart bars 聚合 Daily/Weekly state 的架構會因載入歷史起點不同而走出不同 ATR、pivot 與 BOS 路徑。由於後續 H1 SETUP 必須讀取與 Daily 完全相同的 zones，這不是可接受的顯示差異。

`V10-DZONE-04` 因此將 Daily ATR、confirmed pivot、一次性 BOS、OB source／geometry 與 FVG event 移入單一 Daily `request.security()` context。依 TradingView confirmed HTF 模式，engine outputs 全部位移一根 Daily bar並搭配 `lookahead_on`，Daily/H1 只在 canonical completed-Daily time 改變時消費同一 snapshot；box/line 仍由 chart context 建立。OB/FVG 規則本身不變，本版等待 2324 Daily/H1 exact-zone 回歸。

使用者後續提供 2324 Daily、H4、H1 三張 `V10-DZONE-04` 實圖。三個時框均正常執行，`V10-DZONE-03` 在 H1 缺失的兩個 Bullish OB 已恢復；共同可見區間的主要 Bullish／Bearish OB 與 FVG 上下界第一輪視覺一致，canonical zone feed 可標記為第一輪通過。截圖同時確認 Weekly Bias 仍不一致：Daily Bearish `20/20`、H4 Bullish `16/15`、H1 Bullish `10/10`；因此 zone 通過不代表整個 Weekly→Daily state 已可接 execution。下一步仍需 exact values、失效日、reload／Replay audit，之後再處理 Weekly Bias canonicalization。

## 2026-07-17 - V10 Daily OB 回歸 confirmed BOS 與 Full Range

使用者認為 `V10-DZONE-02` 的 rolling 8-day high／low、只看絕對 body、任意 searchback 反向 K 與 Hybrid Range 不符合預期的傳統 SMC OB。新定義收斂為：獨立 confirmed Daily pivot length 4、完成 close 首次突破舊 pivot、突破 K 方向一致且 body 至少為 Daily ATR(14) × 1.0，再於被突破 pivot 之後回找最近反向非 Doji candle；zone 使用完整來源 K `low → high`。

使用者同時決定 V10 Daily OB 不再由 midpoint 失效，而由完成 Daily close 穿越遠端才失效：Bullish 嚴格低於 bottom，Bearish 嚴格高於 top。這項修改不套用 Daily FVG，也不改 V1／V4 舊架構。Build 升為 `V10-DZONE-03`，在 TradingView compile/runtime 與 Daily／H1 實圖驗證完成前只視為候選。

## 2026-07-16 - V10 新架構開始：Weekly 只負責方向

使用者決定不再以 Weekly OB/FVG 的生成或 touch 作為新架構的交易區域限制。原因是 Weekly zone 生成慢、觸碰少，若仍要求 Weekly touch，加入 Daily 層也無法解除交易次數瓶頸。新目標架構改為 `Weekly Structure Bias → Daily OB/FVG + Daily MSS → H1 execution`：Weekly 管方向、Daily 管位置、H1 管進場。

為避免破壞已驗證的 V1／V4，新增獨立 `smc_weekly_structure_bias_v10.pine`，build 從 `V10-WBIAS-01` 開始。第一階段只實作完成週 K 的 confirmed structure break Bias，尚未加入 Daily zones 或更換 execution gate。V10 暫時保留 V1 複製核心作圖形對照，交易表標示 `LEGACY EXEC`。

Weekly Bias 不由 OB/FVG 推導；它使用 swing length 2，完成 Weekly close 高於先前 confirmed swing high 時轉 Bullish，低於先前 confirmed swing low 時轉 Bearish。每週先判斷舊 pivot、再發布新 pivot。此階段完成後必須先由使用者在 TradingView 驗證 Bias 切換位置，才進入 Daily OB/FVG 第二階段。

`V10-WBIAS-01` 在 2376/Weekly 與 H1 成功執行，兩時框最新 confirmed swing high／low一致，但方向表第一列被 TradingView 左上商品資訊遮住，使用者無法直接辨識目前週多或週空。此問題不改 Bias 核心，升版 `V10-WBIAS-02` 將表格移至圖表中左側，放大目前方向，並增加 swing steplines、flip markers 與可選方向背景。Weekly/H1 的 flip 累計因歷史覆蓋不同不要求一致。

使用者確認 `V10-WBIAS-02` 的紅綠背景與目前週方向表符合「乾淨、簡單、明顯」的需求，但長歷史 swing steplines 使畫面混亂，因此 `V10-WBIAS-03` 只把 swing levels 預設關閉，背景維持開啟。V4 不同步 V10 中間階段：V4 繼續作為 V1 舊架構的穩定核對層；等 V10 的 Daily zones 與新 execution 完成後，再建立獨立的新架構統計版本。

加入 Daily OB/FVG 前，使用者要求先刪除 V10 的 Weekly OB/FVG。`V10-WBIAS-04` 因此不採用只隱藏 boxes 的做法，而是重建成 Weekly Structure Bias-only：刪除 Weekly zone engine、所有 zone arrays／objects／state，以及依賴 Weekly zones 的 legacy execution 與交易表。V1／V4 完整保留舊架構；V10 下一步從乾淨基底新增 Daily zones。

`V10-WBIAS-04` 實圖確認 clean baseline 成功，但因右上沒有任何版本資訊，使用者無法從截圖確認正在測試哪個 build。此問題被定義為永久工作流程規則：V10 及後續新架構候選無論是否已有統計，都必須在最右上保留不可隱藏的 build table；沒有清楚 build ID 的截圖不得作為驗收證據。

`V10-DZONE-01` 從 clean baseline 新增 Daily OB/FVG。Daily zones 使用完成 Daily candles、Daily ATR displacement、OB Hybrid Range、標準三 K FVG 與完成 Daily close midpoint invalidation；第一輪只驗證 zones，不加入 Daily MSS 或 execution。

`V10-DZONE-01` 在 2376/Weekly、Daily、H1 成功執行並顯示永久 build table。使用者要求進一步收斂畫面：左側 Weekly Bias table 應整合到右上未來統計表的最上方。因此 `V10-DZONE-02` 移除左側表，右上固定保留 BUILD 與 Weekly Bias 結構區塊；未來統計只能追加在其下方。

`V10-DZONE-02` 隨後在 2105／Weekly、Daily、H1 完成實圖驗證。三個時框都清楚顯示同一 build、`週空 BEARISH` 與 35.30／29.05 confirmed swing levels；Weekly／Daily flips 為 28／29，H1 因歷史覆蓋較短為 12／13。左側重複表已消失，Weekly 顯示 `USE D / INTRADAY`，Daily/H1 顯示 `ACTIVE`，Daily zones 第一輪跨時框位置一致。

本段沒有策略 rollback。主要顯示問題依序是：左上第一列被商品資訊遮住、swing 線與 zones 疊加造成雜訊、刪除 legacy table 後失去 build ID、最後又出現左／右兩張狀態表分散資訊。最終可重用結論是：淡色 Weekly Bias 背景保留、swing levels 預設關閉、Weekly zones 不回到 V10、BUILD 與 Weekly Bias 永久整合在右上固定頂部區塊。下一步只加入 Daily MSS Bias，不混入 H1 execution。

## 2026-07-16 - ENTRY/TPSL、手機顯示與台股 Long-only 第一輪收斂

本段對話先重新盤點完整 ENTRY／TPSL：ARMED 後回踩固定 break level 並收盤站回才 ENTRY，SL 使用 ARMED 當下的反方向 confirmed H1 pivot。使用者選定兩項簡單調整：ENTRY retest expiry 預設由不限期改為 15 根 H1；TP1 後剩餘部位 SL 移到 Entry。V1 `V1-ENTRYTPSL-01` 先驗證，2324 與 2609 均出現 TP1→BE，預設 50% 於 1R 出場時正確計為 +0.5R；再同步 V4。2609、2324 的 V1/V4 共通績效完成對齊，2376 的最終 current-build 回歸保留為後續安全基準。

接著新增預設開啟的手機統計開關。V1 關閉後隱藏 SIGNAL FUNNEL、只保留交易績效；V4 關閉後只保留 MODEL、Total、TP2 Rate、Net R、Profit Factor。2105/H1 已確認 V1/V4 COMPACT 顯示正常，且 Total 1、TP2 Rate 0%、Net R -1R、Profit Factor 0 一致。此功能只清除 table cells，所有底層計數與策略流程持續運作。

台股方向正式固定為 Long-only。Bearish Weekly OB/FVG 與 Daily bearish MSS/CHOCH 保留作為圖形與風險 context；Bearish OB/FVG 改成兩階淺紅色，但只有 bullish zone + bullish Daily Bias 能進入 SETUP touch gate。方向限制只放在唯一 flow 入口，下游 ARMED、ENTRY、Trade Plan、funnel 與績效因此自然只含多方。2105、2324/H1 的 V1 `V1-LONG-01` 與 V4 `V4-LONG-01` 所有共通欄位完全一致；2324 為 SETUP 16、replaced 4、ARMED 3、Total 2、Net R -0.5R、PF 0.5、OB/FVG 5/11、Same/Changed 8/8，2105 為 8、2、1、1、-1R、0、3/5、3/5。

本輪沒有策略 rollback。兩次靜態 assertion 初次失敗源自檢查式假設錯誤：一次低估 ternary title 內的 build ID 出現次數，一次使用錯誤的 midpoint 變數名稱；修正 assertion 後全部通過，程式本身未因此修改。可重用教訓是版本檢查應核對 indicator 與 table 的實際位置，不只依固定字串數量；V1/V4 仍必須維持「V1 實圖通過後才同步 V4」。

使用者曾評估將 zone 失效由 midpoint 改成完整 zone edge，但決定本輪保留現行規則。因此目前穩定版仍以正式 H1 收盤跌破 bullish midpoint／突破 bearish midpoint 失效；下一對話若重啟此議題，應先記錄 2376 `LONG-01` 基準，再只做 V1 edge-invalidation 候選，不與其他策略調整同時進行。

## 2026-07-15 - ENTRY/TPSL 與 Daily MSS 第一輪邏輯確認完成

本輪先解決完成交易的 SETUP 被同 zone 後續 re-entry 取代：每個確切 Weekly zone 在有效 Trade Plan 建立時立即標記 `traded`，不論最後 WIN／LOSS，同 zone 都不再建立第二筆 SETUP。V1 先驗證完整 `SETUP → ARMED → ENTRY → PLAN` 歷史鏈保留，再同步 V4；2376/H1 的兩者統計完成對齊。

接著處理 Daily MSS 漏訊號。最初發現 D chart 與 H1 聚合 Daily 都可能在判斷 breakout 前先發布本 candle 新 confirmed pivot，因此先建立 `MSS-01` 修正事件順序。TradingView compile 後，原本缺少的 bearish MSS 仍未出現；將 MSS ATR multiplier 暫設為 0 後訊號才出現，證明 ordering 是應保留的正確事件邊界，但不是該案例的直接根因。

最終規格採簡單結構規則：Daily MSS 使用較長 confirmed pivot、完成 Daily close 與 trend reversal，不再要求單根 candle ATR displacement。理由是 Bias 不應因同一結構跌破由一根大 K 或多根中型 K 完成而得到不同方向。V1 `V1-MSS-02` 先在 2376/D 通過圖形驗證，再同步 V4 `V4-MSS-02`。

最終 2376/H1／1095D／FULL 證據：V1/V4 共通欄位均為 SETUP 16、replaced 4、ARMED 3、Total 3、TP2 Rate 33.3%、Net R -0.5R、Profit Factor 0.75、OB/FVG 6/10、Same/Changed 6/10。V4 額外為 UNIQUE SETUP 12、U>A 25.0%、A>T 100.0%。本輪無未解決阻斷問題；本機仍無 Pine compiler，後續任何新規則修改仍須維持 V1 先驗證、再同步 V4。

## 2026-07-14 - Per-zone SETUP 完成與 V1/V4 FULL 對齊

V1 的 `TOUCH` 空白問題最終定位為 Pine v5 `or` 非 lazy evaluation：首次新 zone 的 `fi = -1` 仍會讀取 `array.get(flowStages, -1)`。正確修法是先保存 `fi < 0`，只有 `fi >= 0` 時才讀取 stage。V1 `V1-PZ-03` 先通過 2324、1504/H1 TOUCH 驗證，再以 1504、2105、2324/H1 驗證 FULL 可正常執行。

FULL 初次通過後，畫面標籤少於 SETUP 計數。截圖與 funnel 證明 SETUP 並未漏算；原因是 expiry、Bias flip、Zone invalid 或 replacement 會刪除歷史 SETUP 標籤。`V1-PZ-04` 將此調整為保留最近 40 個歷史 SETUP 標籤，但不改 flow lifecycle、統計或交易判定。使用者已在三個固定標的確認顯示正常且數值不變。

V1 驗證完成後，才將相同負索引修正移植到 `V4-PZ-04`，並把 V1/V4 預設模式設為 `FULL`。最終 TradingView H1／1095D 結果：1504 為 SETUP 20、replaced 9、ARMED 1、Total 0；2105 為 8、0、1、1；2324 為 8、1、1、1。三檔的 OB/FVG、Same/Changed、Net R 與其他共通欄位全部一致。

本輪確認的工作流程是：V1 視覺層先取得實圖證據，再移植相同核心到 V4 統計層；視覺物件差異不得改變訊號結果。Codex 只負責產出 Pine 與本機靜態檢查，TradingView compile／實圖測試由使用者執行。下一階段只處理 ARMED 精修，不同時擴張到 ENTRY 或績效分組。

## 2026-07-14 - Per-zone engine rollback and debugging discipline

Per-zone SETUP/ARMED/ENTRY 同時加入 V1 與 V4 後，兩個指標在 H1 出現完全不顯示的問題。第一次處理錯誤地把重複搜尋與 touch-state 結構視為已確認根因，建立 `V1-PZ-02 / V4-PZ-03` 並預設啟用 `FULL`；TradingView 驗證證明問題仍存在，因此該嘗試已撤回。

當時回到可驗證基準：V1 `V1-PZ-01 / PZ OFF`、V4 `V4-PZ-02 / PZ OFF`。1504、2105、2324 的 H1 均確認兩者可以同時顯示。後續採先測 V1 `TOUCH`、再測 V1 `FULL`，完成 V1 後才同步 V4。版號與 diagnostic mode 必須保留在表格標題，避免截圖與程式版本無法對應。

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
