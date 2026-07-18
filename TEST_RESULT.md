# Test Results

## 2026-07-18 V10-DH1-ENTRY-03 OB/FVG source statistics（首輪TradingView實圖通過）

- 右上表新增OB與FVG兩欄，分別累計SETUP、ARMED、ENTRY、TP1 HIT、TP2、TP1→BE、DIRECT SL與NET R。全域頂部列維持合併顯示。
- TP1 HIT在第一次無SL的1R觸價累計，包含後續TP2或BE；same-bar SL+TP因SL優先，只計DIRECT SL。Trade Plan以保存的zone type回寫結果與R。
- Repository靜態檢查通過：build／永久表`V10-DH1-ENTRY-03`、3×24 table、global row merge、OB/FVG三階段counters、trade source read、TP1／TP2／BE／SL／Net R來源回寫、來源rows、midpoint logic保留、same-bar SL優先、無`strategy.entry()`、V1／V4未修改及`git diff --check`。
- 2317／ETH H1 實圖確認BUILD、SESSION與runtime正常；SETUP `13 + 31 = 44`、ARMED `1 + 13 = 14`、ENTRY `1 + 10 = 11`、TP1 HIT `1 + 5 = 6`、TP2 `1 + 5 = 6`、DIRECT SL `0 + 5 = 5`、NET R `1.5R + 2.5R = 4R`，全部來源加總與全域一致。ARMED `14`、ENTRY `11`且仍有`3 ACTIVE`，符合midpoint pending Buy Limit不立即成交。[2317／ETH H1 ENTRY-03來源統計](docs/images/v10-entry-2026-07-18/2317-eth-h1-entry03-source-stats-pass.png)
- 2105／ETH H1 實圖確認第二組結果亦閉合：SETUP `17 + 19 = 36`、ARMED `2 + 3 = 5`、ENTRY `1 + 3 = 4`、TP1 HIT `0 + 1 = 1`、TP1→BE `0 + 1 = 1`、DIRECT SL `1 + 2 = 3`、NET R `-1R + -1.5R = -2.5R`。[2105／ETH H1 ENTRY-03來源統計](docs/images/v10-entry-2026-07-18/2105-eth-h1-entry03-source-stats-pass.png)
- 首輪驗收結論：ENTRY-03 compile／runtime、來源分類、TP1 HIT包含BE、來源Net R回加及pending數量通過。兩張圖的SAME BAR均為`0`；Weekly Bias改變撤單、same-bar SL+TP紫紅Direct Loss、reload／Replay穩定性仍列補充回歸，不宣稱已逐項通過。

## 2026-07-18 V10-DH1-ENTRY-02 midpoint Buy Limit（Repository靜態通過，待TradingView）

- 修正ENTRY-01需求誤解：ARM成立時保存ARM bar high與final frozen SETUP low，Buy Limit為mintick-rounded midpoint；ARM當根不回填，從下一根ETH H1起只在`low <= limit`時以固定limit成交。
- Pending order無expiry，只由Weekly Bias改變撤單；Daily zone inactive、zone exit與frozen-low break不撤單，zone trimming跳過pending ENTRY。淡青色dashed limit line在ARM建立，成交或撤單時停止。
- Repository靜態檢查通過：title／永久表`V10-DH1-ENTRY-02`、ARM high保存、midpoint公式、later-bar gate、limit touch／limit fill、無next-open entry、無expiry、Weekly cancel、pending trim保護、limit line lifecycle、2-tick SL、1R／2R、same-bar SL優先、fuchsia conflict、無`strategy.entry()`、V1／V4未修改與`git diff --check`。
- 尚待TradingView Pine compile、2317／2324／2634 marker／price／count／result、reload與Replay驗證。

## 2026-07-18 V10-DH1-ENTRY-01 next-H1-open ENTRY（compile/runtime通過，規格淘汰）

- 本版把需求誤解為ARM completed ETH H1後只在下一根H1以open建立ENTRY；不使用break retest／reclaim或ENTRY expiry。
- SL為ARM凍結的final SETUP low下方2 ticks；TP1=1R、TP2=2R、TP1出場50%後SL移到Entry。ENTRY bar立即執行`SL → TP2 → TP1`；同bar SL+TP衝突記Direct Loss並以紫紅marker顯示。
- Repository靜態檢查通過：title／永久表兩處`V10-DH1-ENTRY-01`、`bar_index == armedBar + 1`、Entry=`open`、2-tick SL、固定1R／2R／50%、same-bar conflict、fuchsia marker、無ENTRY expiry、無`strategy.entry()`、entry state push/remove、Trade Plan trim、V1／V4未修改及`git diff --check`。
- 使用者提供2317／ETH H1實圖：BUILD `V10-DH1-ENTRY-01`、W BIAS Bullish、SETUP `44 / 0`、ARMED `14 / 0`、ENTRY／VALID／OPEN `14 / 14 / 0`、TP2／TP1→BE `6 / 3`、Loss／same-bar `5 / 0`、Invalid `0`、Net R `5.5R`、SESSION ETH，未見runtime錯誤。
- 畫面證明程式可執行，也顯示14個ARM全部立即轉成14個ENTRY。使用者隨即澄清正確需求為ARM high與final SETUP low midpoint Buy Limit，因此ENTRY-01規格淘汰，不得作為策略驗收；由ENTRY-02取代。

## 2026-07-18 V10-DH1-ARMED-03 break level diagnostic（ARM 階段收尾）

- 依三檔 ARMED-02 第一輪畫面，只新增預設關閉的 `Show ARM break level`，不修改 break snapshot、waiting lifecycle、close crossover、失效、SETUP／ARMED markers或計數。
- 每個 zone 新增 break line handle；SETUP／lower-low snapshot時建立或移動淡青色水平 dotted line，ARM／hard invalidation／frozen-low invalidation時停止延伸，zone trimming時刪除。Break level 為 `na` 不畫線。
- Build ID 升為 `V10-DH1-ARMED-03`。Repository 靜態檢查通過：title／永久表 build ID、default-off input、display-only reference、break line push/remove/delete、dotted aqua extension、兩個 update paths、三個 stop paths、zone exit不停止診斷、ARM candidate／crossover不變、兩個 canonical ETH requests、no expiry／ENTRY／Trade／performance 及 `git diff --check` 均符合。
- 使用者提供 2317／ETH H1 實圖，右上顯示 `V10-DH1-ARMED-03`、W BIAS Bullish、D ZONES ACTIVE、SETUP `44 / 0 ACTIVE`、ARMED `14 / 0 ACTIVE`與 SESSION ETH，未見 compile／runtime error。
- 開啟診斷後可見多段淡青色 dotted break lines由 snapshot位置水平延伸，並在對應 `B ARMED` transition附近停止；既有 ARM markers與 ARMED-02 的 2317 TOTAL `14` 保持一致，支持顯示層未改變 lifecycle／計數。證據：[2317／ETH H1 ARMED-03 break diagnostic](docs/images/v10-armed-2026-07-18/2317-eth-h1-armed03-break-diagnostic-pass.png)。
- 本張畫面未獨立證明預設關閉 regression、2634 未 ARM的 exact原因、三項取消原因、reload或 Replay；這些保留為 ENTRY 階段的 ARM regression，不擴大寫成已驗證。使用者接受以 ARMED-03 完成 ARM 階段收尾。

## 2026-07-18 V10-DH1-ARMED-02 persistent ARM candidate（第一輪多標的視覺通過）

- 使用者確認行為定義：First-touch SETUP 成立後立即成為 ARM candidate；是否進入 ARMED 只由 ARMED break 邏輯判斷。離開 Daily zone 只停止 lower-low tracking並凍結 low／break，不得取消候選。
- Per-zone state 新增獨立 `waitingForArmed`。Waiting candidate 只有 Weekly Bias 不再 Bullish、Daily zone inactive／移除，或 tracking 已凍結後 completed H1 close 嚴格跌破 frozen SETUP low三個取消來源；不使用 zone exit、re-entry 或時間 expiry。
- 右上 SETUP ACTIVE 改為 waiting candidate 數；ARMED TOTAL／ACTIVE 維持只統計實際完成 transition 的 zones。Build ID 升為 `V10-DH1-ARMED-02`，ENTRY、Trade Plan、績效與 OB/FVG 定義未加入。
- Repository 靜態檢查通過：title／永久表兩處 build ID、waiting state push/remove、每個 SETUP 建立 candidate、三個 waiting 結束路徑、frozen-low 只在 tracking 停止後失效、zone exit 不清 waiting、SETUP ACTIVE 改讀 waiting、兩個 break snapshot、later-bar close crossover、兩個 canonical ETH requests、no expiry／ENTRY／Trade／performance 及 `git diff --check` 均符合。
- 使用者提供 2324、2634、2317／ETH H1 實圖，三者右上均顯示 `V10-DH1-ARMED-02`、W BIAS Bullish、D ZONES ACTIVE與 SESSION ETH，未見 compile／runtime error。SETUP／ARMED 分別為 `24 / 7`、`35 / 9`、`44 / 14`，ARM/SETUP 約 29%／26%／32%，跨標的比例相近。
- 2324 與 2317 畫面可見多個 `B ARMED` 位於 SETUP 後的向上結構推進，使用者判斷目前結果可以；ARMED-02 第一輪多標的 marker／count 視覺通過。2634 可見區間有 SETUP 與後續上漲但沒有清楚 ARM marker，需用 ARMED-03 break line判斷 exact原因；reload／Replay與三項失效逐筆證據仍待完成。

## 2026-07-18 V10-DH1-ARMED-01 simplified per-zone ARMED（實圖暴露 lifecycle 問題）

- 使用者同意 ARMED 先維持簡單：ARMED-01 只由仍 tracking 的 First-touch SETUP 轉入；H1 swing length 3，SETUP 建立時快照最新 confirmed swing high，之後只在 SETUP low 再創低時重新快照，沒有新低時不逐 pivot 追高。
- Completed H1 的 per-zone 順序固定為 hard invalidation → lower-low／break snapshot → close crossover ARMED → 未成立才處理 close 離開 zone，避免突破 zone top 的有效 ARMED bar 先被取消。
- ARMED 成立時保存同一 zone 的 break level、ARMED bar 與 frozen SETUP low，停止 lower-low tracking、暗化原 SETUP marker並顯示一次 `B ARMED`；右上新增 ARMED TOTAL／ACTIVE。ACTIVE 由 Weekly Bias、Daily zone 與 completed H1 close 跌破 frozen low 失效，沒有時間 expiry。
- 本 build 不包含 ENTRY、Trade Plan、績效或 OB/FVG 定義調整；V1／V4 未修改。Repository 靜態檢查通過：indicator／永久表 build ID、10-row table、7 組新增 per-zone arrays push/remove、兩個 break snapshot 寫入點、ARMED-before-zone-exit ordering、later-bar close crossover、frozen-low strict close invalidation、兩個 canonical ETH requests、no ENTRY／Trade／performance 及 `git diff --check` 均符合。
- 使用者提供 2324／ETH H1 實圖，右上顯示正確 build、SESSION ETH、SETUP `24 / 0 ACTIVE` 與 ARMED `0 / 0 ACTIVE`，證明 compile／runtime通過；畫面中的 Bullish Daily OB已有 SETUP，之後價格離開 zone並上漲，卻未形成預期 ARMED。
- 原因為 ARMED-01 只在 `tracking == true` 時檢查 break；第一次 completed H1 close 離開 zone但尚未突破 break level時便把 tracking 關閉，後續真正結構突破不再被評估。此版 lifecycle 不符合需求，由 ARMED-02 取代。

## 2026-07-18 V10-DH1-SETUP-02R1 canonical BOS display dedup（TradingView 通過）

- 使用者提供 2324／ETH H1／`V10-DH1-SETUP-02R1` 實圖；右上 BUILD、`W + D + H1 FIRST TOUCH`、SESSION `ETH` 與 SETUP `24 / 0 ACTIVE` 均清楚可見，證明 Pine compile／runtime 通過。
- 原本約 30.5 的兩條同-event 平行紅色 BOS structure lines 已收斂為一條；畫面其餘 BOS events 亦各維持單一 line／label。`direction + canonical BOS time` display identity 視覺驗收通過。
- SETUP TOTAL 仍為 24，證明顯示去重沒有改變本案例的 First-touch SETUP 累計。使用者同意 SETUP 階段以 R1 收尾，下一階段改做 ARMED。
- 證據：[2324／ETH H1 SETUP-02R1 BOS 去重](docs/images/v10-setup-2026-07-18/2324-eth-h1-setup-02r1-bos-dedup.png)。
- 本圖未逐根證明 First-touch re-entry block、lower-low marker move、reload 或 Replay；這些保留為 ARMED 開發時的 SETUP regression，而不誤寫為本次截圖已涵蓋。

## 2026-07-18 V10-DH1-SETUP-02 First-touch only（H1 基準由 R1 收尾）

- Repository 靜態目標為每個 exact zone 最多一次 SETUP；第一次有效 SETUP 立即 `setupUsed = true`，後續 re-entry 必須被阻擋。
- 2324／ETH H1 實圖確認 build 可執行，SETUP 顯示 `24 / 0 ACTIVE`；First-touch re-entry block、lower-low move、reload／Replay仍未逐項驗證。
- 同一張圖發現 BOS 平行紅線顯示問題，交由 SETUP-02R1 修正；此問題不代表 First-touch state 失敗。
- 本版未加入 ARMED／ENTRY，V1／V4 未修改。

## 2026-07-18 V10-DH1-SETUP-01 lower-low tracking SETUP（實圖可執行，規則被取代）

- Repository 規格已改為 `Weekly Bullish Bias + active Bullish Daily OB/FVG H1 overlap`；Daily MSS、SETUP expiry、ARMED、ENTRY、Trade Plan 與績效不在本 build。
- 程式已加入 exact-zone SETUP state、lower-low marker update、H1 close 離開停止、zone/Weekly Bias 失效停止、re-entry episode 與右上 TOTAL／ACTIVE 顯示。
- 使用者提供 2105／ETH H1 實圖，右上清楚顯示 `V10-DH1-SETUP-01`、SESSION `ETH`、SETUP `184 / 0 ACTIVE`，圖上可見 D FVG 的 `B SETUP` marker，未見 compile/runtime error。
- 實圖同時暴露規格問題：同一 exact zone 離開後 re-entry 會建立新 episode，造成 SETUP TOTAL 膨脹且不符合使用者對同一 SMC zone 的直覺。SETUP-01 因此不再作為保留規則，由 SETUP-02 First-touch only 取代。

## 2026-07-18 V10-FVG-03 isolated 0.50 ATR minimum gap（2105 Daily 視覺通過）

- 本輪依 FVG-02 實圖只把 fixed `dailyFvgMinGapAtrMultiplier` 由 0.10 提高到 0.50，two-tick floor、K2 ATR/body、geometry、metadata、endpoint 與 midpoint invalidation 不變。
- 本版與 2026-07-13 歷史過嚴版本不同：只測試 gap width，不包含 K3 close 順向半部條件，因此可以獨立觀察 0.50 ATR 的影響。
- Repository 靜態檢查通過：build ID、0.50 constant、two-tick floor、Bullish／Bearish gap gate、27 個 confirmed Daily offsets、FVG-01 metadata／endpoint、midpoint invalidation、兩個 ETH canonical requests及 no-execution 均符合；Markdown 相對連結及 `git diff --check` 通過。
- 使用者提供 [2105／Daily FVG-03 實圖](docs/images/v10-fvg-filter-2026-07-18/2105-daily-fvg03-050atr-pass.png)，右上清楚顯示 `V10-FVG-03`、`D ZONES ACTIVE`、`SOURCE ETH`，未見 compile/runtime error。FVG-02 已排除的下方微小 FVG持續消失，約 59.5、由兩條黃虛線標記的上方微小 FVG亦已消失；其他主要 FVG仍可見，使用者判定目前結果合理。
- 結論：`max(K2 ATR × 0.50, 2 ticks)` 在 2105／Daily 的微小 FVG 視覺過濾通過並作為目前 V10 定義保留。這張截圖不證明 2324／2634、ETH H4/H1、K1/K2/K3 exact metadata、跨時框失效 endpoint、reload 或 Replay；上述項目仍不得標記通過。

## 2026-07-18 V10-FVG-02 minimum Daily FVG gap（2105 Daily 部分驗證）

- 本輪只新增固定 `max(K2 Daily ATR × 0.10, syminfo.mintick × 2)` gap width gate，目標是移除使用者在 2105／Daily 最下方 OB 上指出的細小 FVG。
- 這不是已驗證參數。2026-07-13 的舊測試是 `0.5 ATR gap + K3 順向半部` 組合條件，不能單獨證明本版 `0.10 ATR + 2 ticks` 的影響。
- Repository 靜態檢查通過：build ID、固定 constants、Bullish／Bearish gap size、K2 ATR minimum、two-tick floor、27 個 confirmed Daily offsets、FVG-01 metadata／endpoint、midpoint invalidation、兩個 ETH canonical requests與 no-execution 均符合；Markdown 相對連結及 `git diff --check` 通過。
- 使用者提供 [2105／Daily FVG-02 實圖](docs/images/v10-fvg-filter-2026-07-18/2105-daily-fvg02-010atr-partial.png)，右上清楚顯示 `V10-FVG-02`、`D ZONES ACTIVE`、`SOURCE ETH`，未見 compile/runtime error。原先最下方 OB 上的微小 FVG已消失，但使用者以兩條黃虛線標出的約 59.5 上方 FVG仍存在；因此 0.10 ATR gate 只達成部分目標。
- 本截圖未證明 2324／2634、H4/H1、K1/K2/K3 metadata、endpoint 或完整主要 FVG 保留情形；FVG-02 不標記完整通過，後續由 FVG-03 獨立測試 0.50 ATR。

## 2026-07-18 V10-FVG-01 K2 ATR alignment + canonical close-time endpoint（待 TradingView 驗證）

- 本輪依使用者決定只實作兩項 FVG 修正：K2 body 對齊 K2 自身 ATR，以及失效 box／midline 右端使用 canonical Daily `time_close`。使用者不同意 mitigation lifecycle，因此 FVG midpoint invalidation、active boolean 與完整 lifecycle 均維持原狀。
- Canonical Daily FVG event 現在分別傳回 K1 first time、K2 displacement/source time 與 K3 confirmation/event time；box 左端仍使用 K3。標準 wick-to-wick geometry、無最小 gap 寬度、方向條件、顏色與每類 40 個上限不變。
- Repository 靜態檢查通過：indicator／永久表 build ID、兩個 ETH canonical requests、K2 ATR reference、Bull/Bear K2 direction、27 個 Daily confirmed offsets、K1/K2/K3 metadata、canonical close-time invalidation、11 組 zone 平行 arrays push/remove、FVG midpoint 分流及 no-execution 均符合；Markdown 相對連結與 `git diff --check` 通過。
- 尚未執行 TradingView Pine Editor compile/runtime。必須在 2105、2324、2634／Daily、ETH H4、ETH H1 核對 FVG event 差異、K1/K2/K3 時間、top/bottom、midpoint 失效日與三時框右端一致性，未完成前不得標記為通過。

## 2026-07-18 V10-DZONE-09 canonical ETH session（待 TradingView 驗證）

- 2105／2023-12-21 的 Daily 最高與收盤均為 47.90；使用者實圖確認 H1 RTH 看不到 47.90，而 [H1 ETH 實圖](docs/images/v10-eth-session-2026-07-18/2105-h1-eth-dzone08-47.90.png)可見觸及 47.90 的 K 棒。差異定位為 chart session，不是除權息或 OB/BOS source 錯誤。
- 這張收尾截圖右下明確顯示 `ETH`，但右上 BUILD 仍是 `V10-DZONE-08`，且尚無 DZONE-09 新增的 SESSION row。因此它只通過「2105 原生 ETH H1 含 47.90」與「後續統一 ETH 的決策依據」，不代表 `V10-DZONE-09` 已 compile 或視覺通過。
- Weekly／Daily canonical requests 改用同一 `ticker.modify(syminfo.tickerid, session.extended)`；DZONE-08 的 pivot、BOS、extreme opposing source、OB/FVG geometry、失效與 no-execution 均不修改。
- 右上表新增 SESSION：intraday ETH 顯示 `ETH`；非 ETH 顯示 `USE ETH (...)`；Daily 以上顯示 `SOURCE ETH`。此列只診斷原生 chart bars，不能替使用者切換 TradingView session。
- Repository 靜態檢查通過：build ID、兩個 ETH canonical requests、無直接 `request.security(syminfo.tickerid, ...)`、8-row table、SESSION 狀態文字、no-execution、Markdown 相對連結與 `git diff --check` 均符合。仍須 TradingView compile，並用 DZONE-09 在 2105 RTH／ETH、2324、2634 Daily/H4/H1 回歸；未完成前不標記為通過。

## 2026-07-18 V10-DZONE-08 pivot-to-BOS extreme opposing source（待 TradingView 驗證）

- 本輪只修改 V10 Daily OB source K：搜尋區間固定為被突破 confirmed pivot K 之後、BOS K 之前，不包含左右端點，也不再受 8 根 searchback 限制。
- Bullish BOS 只比較區間內 bearish K，以最低 `low` 的 K 作為 OB source；Bearish BOS 只比較 bullish K，以最高 `high` 的 K 作為 source。Doji 排除；同低／同高時取時間較晚、較靠近 BOS 的候選。
- Candidate 在 confirmed pivot 發布時以 pivot 後已完成的確認區間初始化，之後逐根更新，BOS K 本身不進入候選。若區間沒有反向 K，該次 BOS 不建立 OB。
- DZONE-07 已驗證的 BOS line 左端、structure price、右端與 label 保持不變；OB Full Range、ATR/BOS gate、full-edge close invalidation、FVG、canonical Weekly/Daily feed 與 no-execution 亦不修改。
- Repository 靜態檢查通過：build ID、兩個 canonical requests、22 個 Daily confirmed outputs、固定 searchback 移除、反向 K 方向、Bullish 最低 low／Bearish 最高 high、左右端點排除、同價較晚者、無候選不建 OB、BOS line 不變、Full Range、失效分流及 no-execution 均符合；Markdown 相對連結與 `git diff --check` 通過。TradingView compile/runtime、新舊 OB source 差異及 Daily/H4/H1 exact reconciliation 仍待驗證。

## 2026-07-18 V10-DZONE-07 OB BOS structure line（Daily 視覺通過）

- 本輪修正 DZONE-06 的診斷線端點：不再連接 OB source K，而是將形成 OB 的 BOS K 水平連回被突破的 confirmed pivot K。
- Canonical Daily OB event 新增 Bullish／Bearish broken-pivot time 與 price。Bullish line 固定在被突破 swing high；Bearish line 固定在被突破 swing low；左端為 pivot K，右端為 BOS K，顏色預設紅色，BOS K 保留 `BOS ↑／BOS ↓` label。
- `Show Daily OB BOS structure line` 預設開啟；line／label 各最多 40 個。此顯示只在 OB 實際成立時建立，不修改 pivot、BOS、ATR displacement、OB source searchback、Full Range、失效或 canonical feed。
- Repository 靜態檢查通過：build ID、兩個 canonical requests、22 個 Daily confirmed outputs、broken pivot 在新 pivot 發布前保存、水平線雙端共用 structure price、紅色預設、舊 source trace 移除、BOS labels、40 組物件上限、OB searchback／Full Range／失效分流保留及 no-execution 均符合；Markdown 相對連結與 `git diff --check` 通過。
- 使用者提供 2324 與 2634／Daily 實圖，兩者均正常顯示 `V10-DZONE-07`、紅色水平 BOS line 與 `BOS ↑／BOS ↓` labels，並明確回覆圖形符合需求。Daily 視覺通過；H4/H1 exact endpoints 未由本組截圖證明。

## 2026-07-18 V10-DZONE-06 OB BOS-to-source trace（需求不符合）

- 本輪只增加 Daily OB 診斷顯示，不修改 pivot、BOS、ATR displacement、來源 K 搜尋、Full Range、失效或 canonical Daily/Weekly feed。
- 每次 OB 實際建立時，Bullish 由 BOS K 的 low 以左箭頭連到來源 bearish K 的 high；Bearish 由 BOS K 的 high 連到來源 bullish K 的 low。BOS K 分別標示 `BOS ↑`／`BOS ↓`，箭頭左端即本次最多 8 根回找實際停止的來源 K。
- `Show Daily OB BOS-to-source trace` 預設開啟；trace line 與 BOS label 各自最多保留 40 個。Weekly chart 不建立 Daily trace，Daily/H4/H1 使用 canonical completed-Daily time、high、low 與 source time，因此座標來源相同。
- Repository 靜態檢查通過；使用者提供 2324／Daily 實圖，確認 `V10-DZONE-06` 可執行並顯示 BOS label 與斜向來源箭頭。但需求實際是「BOS K 水平回畫至被突破 pivot K」，不是連到 OB source K，因此本版判定需求不符合，由 `V10-DZONE-07` 取代。

## 2026-07-18 V10-DZONE-05 canonical completed-Weekly Bias（跨時框表格通過）

- 本輪只修正 Weekly Bias 資料源一致性：Weekly confirmed pivots、prior-pivot breakout、Bias、Bull/Bear flip counts 與 flip events 全部在單一 Weekly request context 計算。
- Confirmed Weekly snapshot 的 time、Bias、swing high／low、Bull/Bear flips 與兩個 flip events 共 8 個 scalar outputs 全部使用 `[1]`，外層固定 `lookahead_on`；chart context 已無 `currentWeek*`、Weekly OHLC arrays 或 chart-local Bias state。
- `V10-DZONE-04` 的 canonical Daily request、OB/FVG geometry、失效分流與 zone arrays 保留；全程仍無 execution。
- Repository 靜態檢查通過：兩個 canonical requests、8 個 Weekly／16 個 Daily confirmed offsets、chart-driven Weekly/Daily state 移除、Weekly prior-pivot ordering、Bias／flip counts、marker one-shot gate、Daily OB/FVG engine、失效分流、九組 zone 平行 arrays 與 no-execution 均符合；Markdown 相對連結及 `git diff --check` 通過。
- 使用者提供 2324 同一 Replay 位置的 Weekly、Daily、H4、H1 實圖；四個時框右上表均顯示 build `V10-DZONE-05`，且 W BIAS 為 `週多 BULLISH`、W SWING HIGH 為 `47.75`、W SWING LOW 為 `27.50`、W FLIPS B/S 為 `8 / 7`。
- Weekly chart 正確顯示 `D ZONES USE D / INTRADAY`；Daily、H4、H1 正確顯示 `D ZONES ACTIVE`，既有 Daily OB/FVG 仍可見，畫面未出現 compile/runtime 或 memory error。
- 結論：canonical completed-Weekly feed 的四個 table 欄位已通過 Weekly／Daily／H4／H1 跨時框一致性驗證，且 canonical Daily zones 第一輪視覺回歸未受影響。單一 flip marker 是否永遠只發布一次、切換／reload 後狀態，以及 zones 的 source time／精確邊界／失效日／Replay 更新仍待逐項驗證。

## 2026-07-17 V10-DZONE-04 canonical Daily OB/FVG feed（第一輪跨時框視覺通過）

- 本輪只修正 Daily/H1 zone 資料源一致性：Daily ATR、confirmed OB pivot、一次性 BOS、來源 K、OB geometry 與 FVG event 全部在單一 Daily request context 計算。
- Confirmed snapshot 對 16 個 scalar outputs 全部使用 `[1]`，外層固定 `lookahead_on`；chart context 已無 `currentDay*`、Daily OHLC arrays、手動 ATR seed/count 或 chart-local Daily pivot state。
- Daily/H1 只在 canonical Daily time 改變時消費一次相同 snapshot；OB full-edge／FVG midpoint invalidation 分流、OB/FVG geometry、九組 zone 平行 arrays 與 no-execution 保留。
- Repository 靜態 assertions 與 `git diff --check` 通過。
- 使用者提供 2324／Daily、H4、H1 實圖，三個時框均清楚顯示 `V10-DZONE-04`、`D ZONES ACTIVE`，沒有 compile/runtime 或 memory error 畫面。
- `V10-DZONE-03` 在 H1 缺少的約 21.45～21.75、22.65～22.85 Bullish OB 已在 Daily、H4、H1 同時出現；共通可見區間的約 20.65～20.90 Bullish OB、24.00～24.30 Bearish OB及三組主要 FVG 上下界亦第一輪視覺一致。
- 結論：canonical Daily feed 修正通過 2324 Daily/H4/H1 第一輪 zone 顯示回歸。精確 source timestamp／top／bottom 數值、逐區失效日、重新載入與 Replay 逐日更新仍待第二輪核對，不由本次截圖擴大宣稱。
- Weekly Bias 尚未 canonicalize：Daily 顯示 Bearish／flips `20/20`，H4 顯示 Bullish／`16/15`，H1 顯示 Bullish／`10/10`。這不影響本次 zone 通過，但在加入 execution 前仍須修正。

## 2026-07-17 V10-DZONE-03 traditional Daily OB candidate（跨時框驗證失敗）

- 本輪只修改 V10 Daily OB：confirmed pivot length 4、每個 pivot 只接受一次的首次 close BOS、順向 Daily ATR(14) × 1.0 displacement、被突破 pivot 後最近反向來源 K、Full Range 與完成 Daily close full-edge invalidation。
- V10 Daily FVG 保留原三 K geometry、middle-candle displacement、range 與 midpoint invalidation；Weekly Bias、右上永久表及 no-execution state 未修改。
- Repository 靜態檢查已通過：build ID、pivot length 4、rolling lookback 移除、首次 BOS crossing、每 pivot 一次性消耗、方向／ATR gate、來源時間範圍、Full Range、prior-pivot ordering、OB full-edge／FVG midpoint 分流失效、FVG geometry 保留、九組 zone 平行 arrays 生命週期與 no-execution 均符合；Markdown 相對連結及 `git diff --check` 通過。
- 初次交付時尚未執行 TradingView Pine Editor compile、runtime 或實圖驗證；後續使用者回報如下，本版最終判定為跨時框失敗。
- 使用者隨後提供 2324 實圖：Daily 在約 21.45～21.75 與 22.65～22.85 顯示 Bullish OB，H1 同期間沒有這兩個 OB，只保留 FVG；compile/runtime 可執行，但 Daily/H1 zone reconciliation 失敗。
- 同組畫面中 Weekly Bias 為 Daily Bearish、H1 Bullish，flip counts 為 Daily `20/20`、H1 `10/10`。這是兩個圖表時框從不同歷史起點重建高週期 state 的直接證據；OB 並非在 H1 被 full-edge invalidation 刪除，因失效 box 只停止延伸而不會消失。

## 2026-07-16 V10-DZONE-02 integrate Weekly Bias into right table（視覺通過）

- 本輪只調整 table 位置與 row ordering；Daily Zone Engine 與 Weekly Bias 計算未修改。
- Repository 靜態檢查與 `git diff --check` 通過；確認程式只有一個 2×7 右上 table、BUILD 第一列、Weekly Bias 在 phase 之前、左側 table 已移除，Daily engine 與 no-execution state 保留。
- 使用者提供 2105 實圖：[Weekly](docs/images/v10-daily-zones-2026-07-16/2105-weekly-dzone02-right-table.png)、[Daily](docs/images/v10-daily-zones-2026-07-16/2105-daily-dzone02-right-table.png)、[H1](docs/images/v10-daily-zones-2026-07-16/2105-h1-dzone02-right-table.png)。
- 三個時框均清楚顯示 `V10-DZONE-02`，左側 Weekly Bias table 已消失；右上固定順序為 BUILD、W BIAS、W SWING HIGH／LOW、W FLIPS、PHASE、D ZONES。
- 三個時框目前方向均為 `週空 BEARISH`，confirmed swing high／low均為 35.30／29.05。Weekly 與 Daily flips 為 28／29，H1 為 12／13；差異來自可用歷史深度，不影響目前 Bias 與 levels 一致性。
- Weekly 正確顯示 `D ZONES USE D / INTRADAY`；Daily 與 H1 顯示 `ACTIVE`，Daily OB/FVG 在兩個支援時框均可見，第一輪跨時框位置視覺一致。
- Pine Editor compile、runtime、永久版本表、Weekly Bias 整合及 Daily zone 第一輪顯示驗證通過。逐一核對所有歷史 zone 的 midpoint 失效終點仍不在本輪驗收範圍。

## 2026-07-16 V10-DZONE-01 Daily OB/FVG and permanent build table（第一輪視覺通過）

- Repository 靜態檢查與 `git diff --check` 通過；build table、Weekly/Daily 聚合、Daily ATR/OB/FVG、完成 Daily close invalidation、midline default off、無 execution 及 zone 平行 arrays 生命週期均已核對。
- V1 SHA-256 保持 `951AC72372FF6AB75D1830A17C445CA0D57DB85A61F47C31F34AE107ED955B31`；V4 tracked file 未修改。
- 使用者提供 2376 實圖：[Weekly](docs/images/v10-daily-zones-2026-07-16/2376-weekly-dzone01.png)、[Daily](docs/images/v10-daily-zones-2026-07-16/2376-daily-dzone01.png)、[H1](docs/images/v10-daily-zones-2026-07-16/2376-h1-dzone01.png)。
- 三個時框均成功顯示 `V10-DZONE-01`；Weekly 正確顯示 `USE D / INTRADAY`，Daily/H1 顯示 `ACTIVE`。
- Daily OB/FVG 已在 Daily 與 H1 顯示，代表 compile/runtime 與第一輪跨時框視覺通過；逐區 midpoint 失效終點仍待後續精查。
- 本版沒有 execution；畫面出現 SETUP／ARMED／ENTRY 或交易統計即視為錯誤。

## 2026-07-16 V10-WBIAS-04 remove Weekly zones and legacy execution（視覺通過）

- V10 已由約千行的 V1 複製核心重建為 122 行 Weekly Structure Bias-only indicator。
- Repository 靜態檢查與 `git diff --check` 通過；確認固定 Weekly data、Bias core、背景及 swing default off 保留，且程式已無 OB/FVG、zone objects、SETUP／ARMED／ENTRY 或 legacy table。
- 使用者提供 2376/Weekly 實圖：[clean baseline，缺少版本表](docs/images/v10-weekly-bias-2026-07-16/2376-weekly-wbias04-clean-no-version-table.png)。
- Weekly OB/FVG 與 legacy table 已消失，週方向表／背景／markers 正常；使用者確認後要求加入 Daily OB/FVG。
- 畫面缺少可辨識 build ID，無法直接與 Codex 對答案；此發現建立「最右上永久版本識別表」固定規則，從 `V10-DZONE-01` 起強制執行。

## 2026-07-16 V10-WBIAS-03 simplified default display（視覺通過）

- 只將 `Show Weekly Bias swing levels` 預設改為 false；背景維持預設開啟，Weekly Bias 計算未修改。
- Repository 靜態檢查與 `git diff --check` 通過；確認 build ID、swing default off、background default on、Bias core 與 legacy gate 均符合預期。
- 使用者提供 2376/Weekly 實圖：[移除 Weekly zones 前基準](docs/images/v10-weekly-bias-2026-07-16/2376-weekly-wbias03-before-zone-removal.png)。
- `V10-WBIAS-03` compile/runtime 正常，swing levels 預設未顯示，背景與目前方向表正常；使用者下一步要求刪除 Weekly OB/FVG。

## 2026-07-16 V10-WBIAS-02 visible Weekly direction（視覺通過，預設顯示待簡化）

- Repository 靜態檢查與 `git diff --check` 通過；確認中左側方向表、週多／週空文字、兩條 swing steplines、兩種 flip markers、背景開關均存在，且 legacy SETUP gate 未改。
- 使用者提供 2376/Weekly 實圖：[目前週方向與紅綠背景](docs/images/v10-weekly-bias-2026-07-16/2376-weekly-wbias02-visible-direction.png)。
- V10 成功顯示 `週多 BULLISH`，方向表不再被商品資訊遮住；紅綠背景清楚、簡單且獲使用者接受。
- Swing steplines 與歷史 flip markers 疊加在 OB/FVG 上造成畫面混亂；本輪只依使用者要求將 swing levels 預設關閉，markers 保留開關與原預設。
- 核心 Bias 判定未修改；本輪只改善可見性與人工核對能力。

## 2026-07-16 V10-WBIAS-01 independent Weekly Structure Bias（compile/runtime 通過，方向列顯示失敗）

- 新增獨立 V10 Pine 檔；V1／V4 穩定檔未修改。
- Repository 靜態檢查與 `git diff --check` 通過；確認 Weekly Bias 先判斷舊 confirmed pivot、再發布新 pivot，且尚未進入舊 SETUP touch gate。
- 使用者提供 2376 實圖：[Weekly](docs/images/v10-weekly-bias-2026-07-16/2376-weekly-wbias01.png)、[H1](docs/images/v10-weekly-bias-2026-07-16/2376-h1-wbias01.png)。
- V10 在兩個時框均成功執行；confirmed swing high／low同為 402.0／318.5，證明最新 Weekly structure levels 跨時框一致。
- Weekly 的 Bias flips 為 25/25，H1 為 12/12；這符合兩時框可用歷史深度可能不同，flip 累計不可用來判斷目前方向。
- 目前 Bias 所在的表格第一列被 TradingView 左上商品資訊遮住，因此 `V10-WBIAS-01` 的方向顯示驗收失敗，升版 `V10-WBIAS-02` 修正。
- `V10-WBIAS-01` 的 Weekly Bias 尚未接入 SETUP gate；現有交易表為 `LEGACY EXEC`，其 SETUP／ARMED／Total 不代表新架構結果。

## 2026-07-16 V4-LONG-01 Taiwan equity Long-only execution sync（通過）

- V1 驗證通過後，相同 `dir == 1` bullish SETUP touch gate 已同步至 V4 PRIMARY。
- 驗收內容：V4 compile/runtime；2105、2324/H1 的 V1/V4 Total、TP2 Rate、Net R、Profit Factor，以及 FULL 模式的 SETUP、replacement、ARMED、OB/FVG、Same/Changed 全部一致。
- midpoint invalidation、ENTRY/TPSL、compact table 與其他模型公式未修改。
- Repository 靜態檢查與 `git diff --check` 通過：V1/V4 均以 bullish gate 進入唯一 flow push，V4 Bearish zone/MSS、midpoint invalidation 與 compact table 保留。
- 2324/H1／1095D／FULL：V1/V4 共通欄位均為 SETUP 16、SETUP replaced 4、ARMED replaced 0、ARMED 3、Total 2、TP2 Rate 0%、Net R -0.5R、Profit Factor 0.5、OB/FVG 5/11、Same/Changed 8/8。V4 額外為 UNIQUE SETUP 12、U>A 25.0%、A>T 66.7%。
- 2105/H1／1095D／FULL：V1/V4 共通欄位均為 SETUP 8、SETUP replaced 2、ARMED replaced 0、ARMED 1、Total 1、TP2 Rate 0%、Net R -1R、Profit Factor 0、OB/FVG 3/5、Same/Changed 3/5。V4 額外為 UNIQUE SETUP 6、U>A 16.7%、A>T 100.0%。
- 兩個標的所有共通欄位完全一致；V4 compile/runtime 與 Long-only 統計同步通過。

## 2026-07-16 V1-LONG-01 Taiwan equity Long-only execution（通過）

- SETUP touch gate 固定只接受 bullish zone + bullish Daily Bias；Bearish zone 與 bearish Daily structure 仍計算及顯示，但不建立 execution flow。
- 驗收內容：H1 圖上不再出現 S SETUP、S ARMED、S ENTRY、S PLAN；funnel、Total、勝率、Net R、Profit Factor 與 OB/FVG 來源分類只包含多方。
- midpoint invalidation、Bullish flow、ENTRY/TPSL 與 table 顯示均應維持原規則。
- Repository 靜態檢查與 `git diff --check` 通過：唯一 `flowDirs` push 只能由 bullish gate 進入，Bearish zone/MSS 與 midpoint invalidation 保留，short label helpers 雖保留但 execution 不可達。
- 2105/H1：V1 只顯示 B flow，Total 1、Direct Loss 1、Net R -1R、Profit Factor 0；Bearish zone 仍正常顯示。
- 2324/H1：V1 只顯示 B flow，Total 2、TP1→BE 1、Direct Loss 1、Net R -0.5R、Profit Factor 0.5；舊 V4 多空核心為 Total 3、Net R -1.5R、Profit Factor 0.25，差異符合移除空方交易的預期。
- 使用者確認畫面沒有問題並同意同步 V4；V1 Long-only 通過。

## 2026-07-16 V1-BEARVIS-01 bearish zone light-red palette（視覺通過）

- Bearish OB 與 Bearish FVG 仍依原規則建立及失效，只將 box fill、border 與文字改為兩階淺紅色。
- 驗收內容：Bearish OB 與 FVG 均不再出現 olive／深紅，兩者仍可由深淺區分；Bullish zone 與 midline 顏色不變。
- 此版不包含 Long-only 策略修改；SETUP、ARMED、ENTRY、Trade Plan 與統計應與改色前完全一致。
- Repository 靜態檢查與 `git diff --check` 通過：Bearish OB/FVG fill、border、text 均使用新色，V1 已無 `color.olive`，Bullish 配色與 V4 均未修改。
- 使用者提供 2376/Weekly 圖表並確認配色沒有問題；Bearish OB 與 FVG 均為可區分的淺紅色系，視覺通過。

## 2026-07-16 V4-STATS-01 mobile compact statistics sync（通過）

- V1 視覺驗證通過後，相同預設開啟的 `Show SETUP/ARMED/ENTRY statistics` 已同步至 V4。
- 完整模式應維持原 17 欄表格；COMPACT 模式只顯示 MODEL、Total、TP2 Rate、Net R、Profit Factor，MODEL 仍包含 `FULL/PART`。
- 切換前後對應的 Total、TP2 Rate、Net R、Profit Factor 必須完全一致；V4 PRIMARY 底層 arrays 與計算不得改變。
- Repository 靜態檢查與 `git diff --check` 通過：開關預設為 true、完整 17 欄與 compact 5 欄路徑皆存在，compact 直接讀取相同 trade state，策略運算位於顯示條件之外。
- 使用者提供 2105/H1 圖表確認 V4 表頭顯示 `V4-STATS-01 | W-D-H1 | COMPACT`，只保留 MODEL、Total、TP2 Rate、Net R、Profit Factor，MODEL 仍顯示 `FULL`。
- 同畫面 V1/V4 共通績效完全一致：Total 1、TP2 Rate 0%、Net R -1R、Profit Factor 0。V4 compile、runtime、compact 視覺與數值驗證通過。

## 2026-07-16 V1-STATS-01 mobile compact statistics（視覺通過）

- 新增預設開啟的 `Show SETUP/ARMED/ENTRY statistics`。
- 開啟時應維持原完整結果表；關閉時表頭應顯示 `COMPACT`，只保留第 0～10 列交易績效，`SIGNAL FUNNEL` 與 Window 列不顯示。
- 此版為純顯示修改；相同 symbol、Replay 位置及設定下，切換前後 Total、Open、Win TP2、TP1→BE、Direct Loss、TP1/TP2 Rate、Net R、Avg R 與 Profit Factor 必須完全不變。
- Repository 靜態檢查與 `git diff --check` 通過：開關預設為 true、切換時先清空 table、完整／COMPACT row scope 正確，funnel counters 未被顯示條件包住。
- 使用者提供 2105 圖表確認 COMPACT 模式只保留交易績效區，`SIGNAL FUNNEL` 已隱藏，內容符合手機顯示需求；同意同步 V4。

## 2026-07-16 V4-ENTRYTPSL-01 ENTRY expiry and TP1 breakeven sync（部分通過）

- V1 驗證通過後，相同 15 根 H1 ENTRY expiry 與 TP1→BE 核心已同步至 V4 PRIMARY。
- Repository 靜態檢查與 `git diff --check` 通過：V4 build ID、expiry 預設、TP1 後 stop 更新、+0.5R 計算、`SL → TP2 → TP1` 順序及 ENTRY 幾何均符合 V1 核心。
- TradingView Pine Editor compile 與 H1 runtime 通過。
- 2609/H1／1095D／FULL：V1/V4 共通欄位均為 SETUP 23、SETUP replaced 6、ARMED 3、Total 2、TP2 Rate 50.0%、Net R 2R、Profit Factor `-`、OB/FVG 4/19、Same/Changed 10/13。
- 2324/H1／1095D／FULL：V1/V4 共通欄位均為 SETUP 32、SETUP replaced 17、ARMED 3、Total 3、TP2 Rate 0%、Net R -1.5R、Profit Factor 0.25、OB/FVG 8/24、Same/Changed 20/12。
- 兩個標的所有共通欄位一致，證明 V4 的 ENTRY expiry 與 TP1→BE 統計已按 V1 核心運作。2376 新版 V4 尚待回歸，因此本節先標記部分通過。

## 2026-07-16 V1-ENTRYTPSL-01 ENTRY expiry and TP1 breakeven（通過）

- 使用者選定 A＋B：ENTRY retest expiry 預設改為 15 根 H1；TP1 後剩餘部位 SL 移到 Entry。
- 本輪只修改 V1；V4 維持已驗證的 `V4-MSS-02`。
- Repository 靜態檢查與 `git diff --check` 通過：版本標記、expiry 預設、stop array、SL line、TP1→BE R 計算與 `SL → TP2 → TP1` 判定順序均符合規格；舊 `TP1 → Loss` V1 路徑已移除。
- 2376/H1／1095D／FULL 正常執行：V1 為 SETUP 37、ARMED 5、Total 3、Win TP2 2、Direct Loss 1、Net R 2R；V4 舊核心為 Total 4、Net R 1R。V1 `ARMED expired 2`，結果符合 15 根 H1 expiry 排除晚到 ENTRY 的預期。
- 2324/H1／1095D／FULL 正常執行：V1 為 SETUP 30、ARMED 3、Total 3、TP1→BE 1、Direct Loss 2、Net R -1.5R、Profit Factor 0.25；V4 舊核心 Net R -2R。預設 TP1→BE 的 +0.5R 計算正確。
- 2609/H1／1095D／FULL 正常執行：V1 為 Total 2、Win TP2 1、TP1→BE 1、Direct Loss 0、Net R 2R；V4 舊核心 Total 3。ENTRY expiry 與 TP1→BE 皆造成符合規格的結果差異。
- 使用者確認沒有問題並同意同步 V4；V1 本輪通過。首次 TP1 同 K 同時觸及原始 SL 的保守優先順序仍由程式靜態順序確認。

## 2026-07-15 V1-ENTRY-01 one trade per exact zone（通過）

- 使用者在 2376/H1 截圖發現已完成交易保留 ARMED、ENTRY 與 Trade Plan，但原 SETUP 消失；同一 FVG 後續 re-entry 另產生新 SETUP。
- 程式檢查確認：同 zone 新 SETUP 會刪除 active 或 archived SETUP label，而有效 ENTRY 後 flow 已移除，因此原 zone 可再次建立新 flow；這兩項共同造成完整交易鏈被拆開。
- V1 已改為有效 Trade Plan 建立成功時立即將來源 zone 標記 traded／consumed；同 zone 後續不再建立 SETUP，並自然保留原 SETUP／ARMED／ENTRY 歷史鏈。V4 未修改。
- 使用者提供 2376/H1／1095D／FULL 截圖並確認沒有問題：原 `S SETUP → S ARMED → S ENTRY → S PLAN` 完整交易鏈保留，同一 FVG 後續不再產生 SETUP。
- V1 結果為 SETUP 14、SETUP replaced 4、ARMED 3、Valid ENTRY／Total 3、TP2 Rate 33.3%、Net R -0.5R、Profit Factor 0.75、OB/FVG 2/12、Same/Changed 6/8；相較 V4 舊核心 SETUP 17、FVG 15、Same-zone 9，正好排除三筆已交易 zone 的後續 SETUP，ARMED、Total 與績效不變。
- 結論：V1 one-trade-per-exact-zone 與完整交易鏈保留規則通過本次 2376 TradingView 驗證；相同核心可同步 V4。

## 2026-07-15 V4-ENTRY-01 one trade per exact zone（通過）

- V1 通過後，相同 `traded` zone state 已同步至 V4 PRIMARY；有效 Trade 成立時標記 exact zone，同 zone 後續不再建立 SETUP。
- 使用者其後將 Replay 推進至相同最新位置，2376/H1／1095D／FULL 的 V1/V4 共通欄位完全一致：SETUP 21、SETUP replaced 7、ARMED 4、Total 3、TP2 Rate 33.3%、Net R -0.5R、Profit Factor 0.75、OB/FVG 11/10、Same/Changed 12/9。
- V4 額外研究欄位為 UNIQUE SETUP 14、U>A 28.6%、A>T 75.0%；結論：`V4-ENTRY-01` 同步通過。

## 2026-07-15 V1-MSS-01 prior-pivot breakout ordering（待驗證）

- 使用者在 2376/D 發現 bullish MSS 後價格繼續破底，但圖上沒有對應 bearish MSS。
- 程式檢查確認 D chart 與 H1 聚合 Daily 兩條路徑都先寫入本 candle 新 confirmed pivot，再以更新後 pivot 判斷 breakout；若同一 candle 同時確認較低 pivot，舊 pivot 的有效跌破可能因此被遮蔽。
- V1 已改為先用先前 confirmed pivot 判斷 CHOCH/MSS、事件與 Bias，再發布本 candle 新 confirmed pivot；所有參數及其他交易流程保持不變。
- 使用者確認 TradingView compile 通過，但 2376/D 原缺少的 bearish MSS 仍未出現；此結果排除 pivot ordering 為該案例直接根因。
- 將 `D MSS body ATR multiplier` 從 1.0 暫設為 0 後，缺少的 bearish MSS 及其他結構反轉正常出現，確認根因為單根 ATR displacement filter。

## 2026-07-15 V1-MSS-02 close-break Daily MSS（圖形驗證通過）

- 使用者確認 Daily Bias 不應因同一結構跌破由一根大 K 或多根中型 K 完成而不同，選定移除 Daily MSS ATR displacement。
- V1 已移除 MSS ATR inputs、D chart ATR/body 判斷，以及 H1 聚合 Daily 專用的 open／close／True Range arrays 與平均計算；MSS 固定由較長 confirmed pivot、完成 Daily close 與 trend reversal 成立。
- 保留先判斷舊 pivot、後發布新 pivot 的事件順序；Weekly OB/FVG ATR、Bias invalidation 與完整交易流程未修改。
- 使用者在 2376/D 確認正式版 bearish／bullish MSS 正常出現並接受圖形結果；V1 可同步至 V4。
- 後續 `V4-MSS-02` 同步完成，2376/H1 的 V1/V4 共通統計回歸結果見下一節，已通過。

## 2026-07-15 V4-MSS-02 close-break Daily MSS sync（通過）

- V1 圖形驗證後，相同 Daily MSS 核心已同步至 V4 PRIMARY：移除 MSS ATR inputs、body／True Range 計算與相關 arrays。
- V4 完成 Daily candle 先以先前 confirmed pivot 判斷 close reversal、MSS、Bias 與 invalidation，再發布本 candle 新確認的 pivot。
- Weekly OB/FVG ATR、15 根 H1 SETUP expiry、per-zone ENTRY、TPSL 與績效公式均未修改。
- 使用者提供 2376/H1／1095D／FULL 同畫面截圖，V1/V4 共通欄位完全一致：SETUP 16、SETUP replaced 4、ARMED replaced 0、ARMED 3、Total 3、TP2 Rate 33.3%、Net R -0.5R、Profit Factor 0.75、OB/FVG 6/10、Same/Changed 6/10。
- V4 額外研究欄位為 UNIQUE SETUP 12、U>A 25.0%、A>T 100.0%；沒有發現同步差異，`V4-MSS-02` 通過。

## 2026-07-15 V1-AR-02S1 latest SETUP label per zone 通過

- 使用者提供 2376、1504、2105、2324/H1／1095D／FULL 截圖；同一個確切 zone 的歷史 SETUP 已收斂為最新一個標籤，2376 畫面由多個同 zone 標籤明顯簡化。
- 2376 的 V1/V4 統計仍一致：SETUP 24、SETUP replaced 7、ARMED 4、Total 3、Net R -0.5R、OB/FVG 11/13、Same/Changed 15/9。
- 1504 維持 SETUP 19、replaced 8、ARMED 1、Total 1、Net R 1.5R；2105 維持 SETUP 8、replaced 0、ARMED 0、Total 0、Net R 0R；2324 維持 SETUP 8、replaced 1、ARMED 1、Total 0、Net R 0R。
- 結論：每個 zone 只顯示最新 SETUP 的純視覺修改通過，未改變 active flow、replacement、ARMED、Total、來源分類或績效。

## 2026-07-14 V1-AR-02 fixed pre-SETUP pivot（結果混合）

- 使用者提供 1504、2105、2324/H1／1095D／FULL 截圖；`V1-AR-02` 三檔均正常顯示，未見空白或 runtime 問題。
- 1504：SETUP 19、SETUP replaced 8、ARMED 1、Total 1、Win TP2 1、Net R 1.5R；相較 `V1-AR-01` 的 SETUP 20、ARMED 1、Total 0，固定 break level 改變 lifecycle 並形成一筆完成交易。
- 2105：SETUP 8、SETUP replaced 0、ARMED 0、Total 0；相較 `V1-AR-01` 的 ARMED 1、Total 1，固定於 SETUP 當下的舊 pivot 未在期限內形成有效收盤穿越。
- 2324：SETUP 8、SETUP replaced 1、ARMED 1、Valid ENTRY 0、Total 0；相較 `V1-AR-01` 的 ARMED 1、Total 1，ARMED 仍存在但後續流程不同。
- 結論：固定 pre-SETUP pivot 並取消 ATR displacement 沒有一致增加 ARMED，但三檔結果由 `0R／-1R／-1R` 改為 `1.5R／0R／0R`；它更早完成 1504 並避開 2105、2324 的舊版虧損流程，初步結果正面。因截圖尚不足以逐筆確認 ARMED 位置是否符合使用者直覺，暫不宣告通過，也不同步 V4。
- 使用者追加 2376 與 2002 截圖並認可「突破固定結構即視為有動能，剩下交給市場與統計」：2376 的 `V1-AR-02` 為 SETUP 24、ARMED 4、Total 3、Net R -0.5R，相較畫面 `V4-AR-01` 的 SETUP 21、ARMED 7、Total 6、Net R -1.5R；2002 的 V1/V4 舊基準結果同為 SETUP 16、ARMED 1、Total 1、Net R 1.5R。
- 使用者接受 `V1-AR-02` 的 ARMED 邏輯與視覺位置；相同 fixed pre-SETUP pivot／close crossover／no ATR displacement 核心已同步為 `V4-AR-02`，尚待 TradingView 對齊驗證。
- `V4-AR-02` 實圖驗證：1504 與 V1 同為 SETUP 19、SETUP replaced 8、ARMED 1、Total 1、Net R 1.5R；2105 同為 SETUP 8、ARMED 0、Total 0、Net R 0R，兩檔通過。
- 2324 發現差異：V1 為 ARMED 1、Total 0、Net R 0R，V4 為 ARMED 1、Total 1、Net R -1R。定位為同一根 H1 的執行順序不同：V4 先寫入當根新 confirmed pivot 再建立 SETUP，V1 先建立 SETUP再更新 pivot，兩者因此凍結不同 break level。
- V4 已調整為與 V1 相同的 SETUP-before-pivot-update 順序，build ID 為 `V4-AR-02R1`；尚待 2324 重驗，並回歸 1504、2105。
- 使用者提供 2324/H1／1095D／FULL 的 `V4-AR-02R1` 截圖；V1/V4 同為 SETUP 8、SETUP replaced 1、ARMED replaced 0、ARMED 1、Total 0、Net R 0R、OB/FVG 2/6、Same/Changed 3/5，2324 修正驗證通過。
- `V4-AR-02R1` 尚待 1504、2105 快速回歸後，才宣告 ARMED 本輪完整對齊完成。
- 使用者完成 1504、2105 回歸：1504 的 V1/V4 同為 SETUP 19、SETUP replaced 8、ARMED 1、Total 1、TP2 Rate 100%、Net R 1.5R、OB/FVG 6/13、Same/Changed 10/9；2105 同為 SETUP 8、SETUP replaced 0、ARMED 0、Total 0、Net R 0R、OB/FVG 4/4、Same/Changed 1/7。
- 結論：`V1-AR-02` 與 `V4-AR-02R1` 已在 1504、2105、2324/H1／1095D／FULL 完成所有共通欄位對齊；fixed pre-SETUP pivot ARMED 本輪驗證通過。

## 2026-07-14 V1-AR-01 ARMED A＋B 驗證通過

- 使用者提供 1504、2105、2324/H1／1095D／FULL 截圖；三檔均顯示 `V1-AR-01` 表格、Weekly zones 與歷史 SETUP，未出現空白或可見 runtime 問題。
- 1504：SETUP 20、SETUP replaced 9、ARMED 1、ARMED replaced 0、Total 0。
- 2105：SETUP 8、SETUP replaced 0、ARMED 1、ARMED replaced 0、Total 1。
- 2324：SETUP 8、SETUP replaced 1、ARMED 1、ARMED replaced 0、Total 1。
- 三檔 SETUP、ARMED、Total 與畫面中的 `V4-PZ-04` 舊基準一致；將 ARMED displacement 由 H1 ATR(14) × 1.0 降為 × 0.5，未在本組樣本增加 ARMED。
- V1 驗證通過後，已將同一個 15 根 H1 expiry 與 ATR × 0.5 核心同步至 `V4-AR-01`；V4 尚待 TradingView compile、runtime 與逐欄對齊驗證。
- 使用者其後提供 `V4-AR-01` 三檔截圖；1504 為 SETUP 20、Unique 11、SETUP replaced 9、ARMED replaced 0、ARMED 1、Total 0，2105 為 8、8、0、0、1、1，2324 為 8、7、1、0、1、1。
- V4 在三檔均正常顯示，SETUP、SETUP replaced、ARMED replaced、ARMED、Total、OB/FVG 與 Same/Changed-zone 等所有 V1 共通欄位完全一致；`V1-AR-01`／`V4-AR-01` ARMED A＋B 對齊驗證通過。

## 2026-07-14 V1-PZ-04／V4-PZ-04 FULL 對齊通過

- 使用者在 TradingView H1／1095D 啟用 `V1-PZ-03 / FULL`，1504、2105、2324 均正常顯示，未發生空白或 crash。
- 1504：SETUP 20、ARMED 1、Total 0；2105：SETUP 8、ARMED 1、Total 1；2324：SETUP 8、ARMED 1、Total 1。
- FULL 畫面的歷史 SETUP 標籤少於 SETUP 計數，已定位為 flow 清理時刪除標籤；不是 SETUP 漏判。
- 使用者提供 1504、2105、2324/H1 的 `V1-PZ-04 / FULL` 截圖，確認歷史 SETUP 已保留，三檔 SETUP／ARMED／Total 與修改前一致；V1 顯示修正通過。
- `V4-PZ-04` 已同步 V1 的兩段式負索引檢查並預設 `FULL`；TradingView compile／runtime 通過。
- 1504：V1/V4 均為 SETUP 20、SETUP replaced 9、ARMED 1、Total 0、OB/FVG 6/14、Same/Changed 11/9、Net R 0R。
- 2105：V1/V4 均為 SETUP 8、SETUP replaced 0、ARMED 1、Total 1、OB/FVG 4/4、Same/Changed 1/7、Net R -1R。
- 2324：V1/V4 均為 SETUP 8、SETUP replaced 1、ARMED 1、Total 1、OB/FVG 2/6、Same/Changed 3/5、Net R -1R。
- 結論：三檔所有 V1/V4 共通欄位一致，SETUP 階段完成，可進入 ARMED 精修。V4 的 UNIQUE SETUP、U>A、A>T 為額外研究欄位。

## 2026-07-14 Per-zone SETUP flows（驗證失敗，待定位）

- V1 與 V4 PRIMARY 曾加入依 Weekly zone key 保存多候選平行 arrays 的草稿；這是目標實作，不代表 TradingView 已可正常執行。
- SETUP expiry 預設改為 15 根 H1；re-entry 只替換同 zone 尚在 SETUP 的流程，已 ARMED 不受新 touch 影響。
- Zone 失效或其他候選失效會刪除尚未完成的 SETUP／ARMED；完成 ENTRY 後的 Trade Plan 與統計保留。
- Repository 靜態內容檢查與 `git diff --check` 已執行；仍需 TradingView Pine Editor compile，以及 2105、1504、2324 的 V1/V4 逐欄核對。
- 第一輪 V1 在 1504 H1 沒有顯示任何物件或表格，TradingView 未提供可見錯誤；限制 touch-state 數量後仍未恢復。
- 第二輪改用 Zone 同索引 touch state 與較少搜尋後，H1 `FULL` 仍完全不顯示。這證明先前的效能根因只是推測，不是已確認結論；相關 PZ-02/PZ-03 嘗試已撤回。

## 2026-07-14 V4 H1 direct execution

- V4 PRIMARY 已由 H4 data carrier + H1 arrays 改為直接在 H1 chart 使用完成圖表 bars；V1 與 V4 現可設在同一 H1 畫面。
- 兩個 LEGACY 模型已停止計算，表格只顯示 `OFF`；本次沒有重構其 H4 context。
- Repository 靜態檢查與 `git diff --check` 已通過：V4 僅允許 H1、PRIMARY 直接使用圖表 OHLC/pivot/ATR、lower-timeframe requests 與 H4 gate 均已移除。
- 使用者已在同一張 H1 chart 同時顯示 V1 與 V4，並完成 2105、1504、2324 重新核對；三個標的的所有 V1 可比欄位均與 V4 PRIMARY 完全一致。
- 2105：SETUP 8、SETUP replaced 0、ARMED replaced 0、ARMED 2、Total 1、TP2 Rate 0%、Net R -1R、Profit Factor 0、OB/FVG 4/4、Same/Changed 1/7。
- 1504：SETUP 20、SETUP replaced 10、ARMED replaced 1、ARMED 2、Total 1、TP2 Rate 0%、Net R -1R、Profit Factor 0、OB/FVG 6/14、Same/Changed 11/9。
- 2324：SETUP 8、SETUP replaced 2、ARMED replaced 0、ARMED 1、Total 1、TP2 Rate 0%、Net R -1R、Profit Factor 0、OB/FVG 2/6、Same/Changed 3/5。
- 結果：V4 H1 直接執行版成功執行並通過三標的一致性驗收；V1/V4 現可在同一 H1 畫面直接比較。LEGACY OFF 不在驗收範圍內。

## 2026-07-14 W-D-H1 primary migration

- V1 已限制為只在 H1 chart 建立 SETUP／ARMED／ENTRY；其他 chart 顯示 `Use H1 chart`。
- V4 第一列已由 H4 execution 改為逐根回放 H1 arrays 的 `PRIMARY W-D-H1`；H4 chart 僅作 data carrier。另兩列標記為 LEGACY，不列入本輪一致性結論。
- Repository 靜態檢查與 `git diff --check` 已通過；確認 V1 只允許 H1、V4 PRIMARY 使用 H1 arrays、舊 H4 SWING 呼叫已移除、PRIMARY 的 24 小時 expiry 換算為 24 根 H1。TradingView Pine Editor compile 及 2105、1504、2324 的逐欄一致性尚未驗證。
- 使用者已在 TradingView 成功載入新版 V1 與 V4；2105／1095D 的 V1 H1 與 V4 H4 data-carrier `PRIMARY W-D-H1` 完全一致：SETUP 8、SETUP replaced 0、ARMED replaced 0、ARMED 2、Total 1、TP2 Rate 0%、Net R -1R、Profit Factor 0、OB SETUP 4、FVG SETUP 4、Same-zone SETUP 1、Changed-zone SETUP 7。V4 額外欄位為 UNIQUE SETUP 8、U>A 25%、A>T 50%。
- 2105 畫面亦確認 V1 在 H4 顯示 `Use H1 chart`，而 V4 在 H1 顯示 `USE H4 CHART`，兩支程式的正式執行入口提示正確。
- 2324／1095D 的 V1 H1 與 V4 `PRIMARY W-D-H1` 完全一致：SETUP 8、SETUP replaced 2、ARMED replaced 0、ARMED 1、Total 1、TP2 Rate 0%、Net R -1R、Profit Factor 0、OB SETUP 2、FVG SETUP 6、Same-zone SETUP 3、Changed-zone SETUP 5。V4 額外欄位為 UNIQUE SETUP 6、U>A 16.7%、A>T 100%。
- 2324 的 V1 詳細 funnel 另顯示 Valid ENTRY 1、Direct Loss 1、SETUP bias flip 1、SETUP zone invalid 5，與上述 PRIMARY 統計流程相符。
- 1504／1095D 的 V1 H1 與 V4 `PRIMARY W-D-H1` 完全一致：SETUP 20、SETUP replaced 10、ARMED replaced 1、ARMED 2、Total 1、TP2 Rate 0%、Net R -1R、Profit Factor 0、OB SETUP 6、FVG SETUP 14、Same-zone SETUP 11、Changed-zone SETUP 9。V4 額外欄位為 UNIQUE SETUP 10、U>A 20%、A>T 50%。
- 1504 的 V1 詳細 funnel另顯示 Valid ENTRY 1、Direct Loss 1、SETUP bias flip 2、SETUP zone invalid 6，與上述 PRIMARY 統計流程相符。
- 結果：新版 V1 與 V4 均成功在 TradingView 執行；2105、1504、2324 的所有 V1 可比欄位均與 V4 `PRIMARY W-D-H1` 一致。本輪 W-D-H1 遷移與一致性驗收通過；兩列 LEGACY 不在結論範圍內。

## 2026-07-14 Daily structure display scope

- V1 已停止在 H4/H1/M30 繪製聚合 Daily CHOCH/MSS 線與文字；intraday 聚合、Daily Bias 更新及 SETUP 判定保留不變。
- 已完成 Repository 靜態檢查；使用者提供的 TWSE:2324 TradingView 截圖確認 Daily chart 仍正常顯示 Daily MSS。
- H4 chart 不再顯示 Daily CHOCH/MSS，以及修改前後 H4 SETUP 統計一致性，仍需以修改後畫面／數據獨立確認。

## Weekly Zone 視覺證據（2026-07-13）

- 修改前難以解釋的寬大／重疊 OB：[ob-before.png](docs/images/weekly-zone-2026-07-13/ob-before.png)
- FVG 過度篩選、肉眼可辨識 gap 未顯示：[fvg-overfiltered.png](docs/images/weekly-zone-2026-07-13/fvg-overfiltered.png)
- 現行版 H4／1095D 最終驗收：[2105](docs/images/weekly-zone-2026-07-13/final-2105.png)、[1504](docs/images/weekly-zone-2026-07-13/final-1504.png)、[2324](docs/images/weekly-zone-2026-07-13/final-2324.png)
- 圖片只保留能說明規則變更原因與最終結果的代表案例；精確統計仍以下方文字紀錄為準。

## 2026-07-13 Daily Bias responsiveness and structural invalidation

- V1 與 V4 SWING 的 Daily MSS 預設 swing length 已由 5 改為 4；ATR(14) × 1.0 displacement 與 trend-reversal MSS 條件不變。
- Bullish／Bearish MSS 現在會分別固定保存成立當下最新的 confirmed Daily swing low／high；後續完成 Daily close 穿越時，Bias 轉為 Neutral。失效位不 trailing，CHOCH 與時間期限不參與 Bias 取消。
- 已完成 Repository 靜態交叉檢查與 `git diff --check`；使用者已確認 V1、V4 的 `D MSS swing length` 均為 4，兩支程式也都能在 TradingView H4／1095D 執行。
- 第一輪固定案例：2324 的 V1／V4 SWING 完全一致，均為 SETUP 8、OB SETUP 2、FVG SETUP 6、Same-zone SETUP 4、Changed-zone SETUP 4、ARMED 0、Total 0。
- 2105 的 V1 為 SETUP 8／OB 6／FVG 2／Same 2／Changed 6，V4 SWING 為 9／6／3／2／7；1504 的 V1 為 18／4／14／11／7，V4 SWING 為 19／4／15／11／8。兩個差異均為 V4 多 1 個 FVG／Changed-zone SETUP，ARMED 與 Total 不受影響。
- 程式碼交叉比對定位到 V1 新增的直接 Daily 失效判斷缺少 `timeframe.isdaily` 限制，導致 H4 chart 每根 H4 close 都可能提前將 Bias 轉為 Neutral；V4 SWING 則正確地只在完成 Daily candle 後判斷。V1 已補上 timeframe 限制，Intraday chart 僅保留聚合完成 Daily candle 的失效路徑。
- 修正後第二輪 TradingView H4／1095D 實圖：2105 的 V1／V4 SWING 均為 SETUP 9、SETUP replaced 1、ARMED 0、Total 0、OB SETUP 6、FVG SETUP 3、Same-zone SETUP 2、Changed-zone SETUP 7、Net R 0R。
- 1504 的 V1／V4 SWING 均為 SETUP 19、SETUP replaced 4、ARMED 1、Total 1、OB SETUP 4、FVG SETUP 15、Same-zone SETUP 11、Changed-zone SETUP 8、Net R -1R、Profit Factor 0。
- 2324 的 V1／V4 SWING 均為 SETUP 8、SETUP replaced 2、ARMED 0、Total 0、OB SETUP 2、FVG SETUP 6、Same-zone SETUP 4、Changed-zone SETUP 4、Net R 0R。
- 結果：兩支程式均成功執行；三個固定標的的所有 V1 可比欄位均與 V4 SWING 一致。Daily MSS swing length 4 與固定結構失效位本輪驗收通過。

## 2026-07-13 FVG ATR filtering

- 現行規則已同步收斂為：標準三根完成 K 的 wick-to-wick gap，不設最小 gap 寬度；中間 candle 必須與 FVG 同方向且 body 至少為來源時框 Wilder ATR(14) × 1.0。V1 Weekly 與 V4 SWING Weekly 共用相同 geometry、ATR、方向與 middle displacement 公式。
- 先前測試的 ATR × 0.5 gap 寬度與確認 K 順向半部條件，因圖表上排除大量肉眼可辨識的標準 FVG 而移除；下列 2105／1504／2324 數據只保留為該過嚴版本的歷史對照，不代表現行規則結果。
- 過嚴版本歷史結果：2105／1504／2324 的 FVG SETUP 為 3／4／17，總數 24；三檔 SWING 均無 ARMED／Trade。
- 使用者已在 TradingView H4／1095D 載入現行簡化版 V1 與 V4，兩版均成功顯示結果，視為 Pine Editor compile／執行通過。
- 2105：V1 與 V4 SWING 同為 SETUP 17、SETUP replaced 6、ARMED replaced 0、ARMED 0、Total 0、OB SETUP 6、FVG SETUP 11、Same-zone SETUP 9、Changed-zone SETUP 8、Net R 0R。
- 1504：V1 與 V4 SWING 同為 SETUP 22、SETUP replaced 7、ARMED replaced 0、ARMED 1、Total 1、OB SETUP 4、FVG SETUP 18、Same-zone SETUP 14、Changed-zone SETUP 8、TP2 Rate 0%、Net R -1R、Profit Factor 0。
- 2324：V1 與 V4 SWING 同為 SETUP 19、SETUP replaced 10、ARMED replaced 0、ARMED 0、Total 0、OB SETUP 2、FVG SETUP 17、Same-zone SETUP 15、Changed-zone SETUP 4、Net R 0R。
- 現行結果：三個固定標的的所有 V1 可比欄位均與 V4 SWING 一致。相較只完成 OB 修改、尚未加入 FVG displacement 的 13／27／23，現行 FVG SETUP 為 11／18／17，總數由 63 降至 46，約減少 27%；1504 恢復 1 次 ARMED、1 次交易。這只能證明篩選程度較前版溫和且兩版一致，策略品質仍需完整股票池統計判斷。

## 2026-07-13 OB displacement and Hybrid Range

- 已同步修改 V1 與 V4：OB 結構突破 candle body 固定要求來源時框 Wilder ATR(14) × 1.0，並固定使用 Hybrid Range；V1 Weekly 與 V4 SWING Weekly 的 ATR、displacement、來源 candle 搜尋及上下界公式相同。
- 已完成 Repository 靜態交叉檢查與 `git diff --check`；舊的 `Use full candle wick for OB range`／`useWicks` 路徑已移除。使用者已在 TradingView H4／1095D 載入新版 V1 與 V4，兩版均成功顯示結果，視為 Pine Editor compile／執行通過。
- 2105：V1 與 V4 SWING 同為 SETUP 19、SETUP replaced 7、ARMED replaced 0、ARMED 0、Total 0、OB SETUP 6、FVG SETUP 13、Same-zone SETUP 10、Changed-zone SETUP 9、Net R 0R。
- 1504：V1 與 V4 SWING 同為 SETUP 31、SETUP replaced 11、ARMED replaced 0、ARMED 1、Total 1、OB SETUP 4、FVG SETUP 27、Same-zone SETUP 21、Changed-zone SETUP 10、TP2 Rate 0%、Net R -1R、Profit Factor 0。
- 2324：V1 與 V4 SWING 同為 SETUP 25、SETUP replaced 12、ARMED replaced 1、ARMED 1、Total 0、OB SETUP 2、FVG SETUP 23、Same-zone SETUP 17、Changed-zone SETUP 8、Net R 0R。
- 結果：三個固定標的的所有 V1 可比欄位均與 V4 SWING 一致，新版 OB displacement／Hybrid Range 的程式交叉核對通過。OB 圖形品質是否達到策略需求仍屬視覺與後續統計評估，不由欄位一致性單獨判定。

## V4 Top-down Model Research Engine

- 狀態：使用者已於 TradingView Pine Editor 成功 compile 並儲存現行 V4。第一版的 consistency warnings 與多 Zone 初版的回傳型別錯誤均已修正；現行版已可在 H4 chart 顯示三套模型資料。
- 已驗證：2105、1504、2324 的 H4／1095D `SWING W-D-H4` 所有 V1 可比欄位完全一致。
- 待驗證：完成 candle 邊界、三套模型獨立 state、Unique SETUP conversion，以及 INTRADAY／FAST 的逐筆參考基準。

### 2026-07-13 H4 chart 第一輪三標的實圖（舊單一 Zone V4）

- TWSE:2105：SWING `37/27/11/2/0`、INTRADAY `90/51/42/16/9`、FAST `127/74/57/21/6`（欄位依序為 Raw/Unique/Repl/Armed/Trades）；Net R 分別為 `0R / 3R / -4R`，三列均顯示 `FULL 1095D`。
- TWSE:1504：SWING `19/18/1/0/0`、INTRADAY `89/50/40/12/4`、FAST `146/99/50/30/12`；Net R 分別為 `0R / -1.5R / 0.5R`，三列均顯示 `FULL 1095D`。
- TWSE:2324：SWING `46/28/19/1/0`、INTRADAY `101/54/53/17/5`、FAST `149/88/65/26/11`；Net R 分別為 `0R / -3R / -8R`，三列均顯示 `FULL 1095D`。
- 初步觀察：三套 Top-down models 已呈現明顯不同的訊號密度與績效；SWING 在三檔皆幾乎無交易，INTRADAY 在 2105 相對突出，FAST 在 1504 僅小幅為正，2324 三套模型均無正報酬。
- 這組結果來自升級前的單一最新 Zone Engine，只保留作歷史對照，不得用於多 Zone V4 模型選擇。
- V4 已改用與 V1 相同規則的多 Zone arrays；現行版已重新 compile，並完成相同 symbol/H4/1095D 的 V1／V4 SWING 統計對照。
- 多 Zone 初版曾於 `trimZoneType()` 出現 `series int; void` 分支回傳型別錯誤；現行版已移除不相容結構，純副作用函式固定回傳 `true`，TradingView compile 已通過。

## 2026-07-12 V3 long-window research mode

- V3 統計 Window 已改為 1095D／1825D／2555D；1825D 與 2555D 在非 H4 chart 顯示 `USE H4 CHART`。
- V3 已固定關閉 zone、structure、SETUP/ARMED/ENTRY 與 Trade Plan 繪圖；OB/FVG 仍以純數值 arrays 維護，供三套統計引擎判斷。
- `request.security_lower_tf()` 已明確使用 Essential 可用的 `calc_bars_count=100000`。
- M30、H1、H4 已分別追蹤第一筆資料時間並顯示 `FULL`／`PART`；全部通過才顯示 `3TF V3 FULL`。
- 已完成 Repository 靜態檢查。使用者實圖確認修改後的 V3 可在 TWSE:2317 H4 chart 執行，1825D 與 2555D 均顯示 H4/H1/M30 `FULL`，可視為本次 symbol/方案/圖表的 compile 與 coverage 顯示通過。
- 本次尚未驗證：1095D、其他台股、實際資料起訖日期、warm-up、中間缺失 bars、執行時間與記憶體壓力，以及修改後 V3 與 V1 的逐筆／統計一致性；不可由 2317 單一結果推廣至所有台股。

## 2026-07-11 V1 chart-driven Entry timeframe

- V1 已將 Trade Plan 計算與繪圖解耦；靜態檢查確認有效 ENTRY 無條件建立數值 Trade Plan，顯示開關只包住 line/label 建立與更新。尚待 Pine Editor compile，以及開關前後統計數值一致性確認。
- V1 已移除手動 `Entry timeframe` input，改由 H4/H1/M30 chart timeframe 自動決定單引擎週期；其他交易、統計與繪圖流程未改動。
- 已完成 Repository 靜態檢查；使用者實圖已確認 H4/H1/M30 表格標題與對應 V3 統計一致。V1 解耦修改後尚待 Pine Editor compile，以及開關前後統計數值一致性確認。

## 2026-07-11 Cross-Timeframe Stats v3

- 使用者在 9921、365D Window 的 H4、H1、M30 圖表完成實圖比對：V1 與 V3 對應列分別為 H4 `SETUP 17 / ARMED 1 / Trades 1 / Net R 0R`、H1 `20 / 2 / 1 / 1.5R`、M30 `26 / 6 / 4 / 2R`；TP1%、TP2%、Avg R、PF 亦一致。
- V3 三引擎重構後已在 Pine Editor compile；開發過程發現並修正條件 scope consistency 警告及破損字串 syntax error。上述編譯與實圖結果可標記為本次樣本通過。
- 單引擎草稿曾完成 Pine Editor compile並回報三項 consistency 警告；三引擎重構已將 chart-side pivot high、pivot low 與 ATR 保持在全域逐次計算。
- V3 保留 V2 的 M30/H1/H4 三套獨立狀態與交易 arrays；M30 engine 在 H1/H4 chart 回放 M30 arrays，H1 engine 在 H4 chart 回放 H1 arrays，H4 engine 在完成 H4 時更新。
- Repository 靜態檢查包含：兩個 `request.security_lower_tf()` expression 均不含 line/label/box、三套 SETUP/ARMED/Trade arrays 分離、三列表格存在，以及 `git diff --check` 無 whitespace error。
- 未實際測試：其他 symbol/session、90/180/730D Window、`3TF PARTIAL` 覆蓋警告、長時間 Replay 與 arrays/request/object limits；這些項目不標記為通過。

## 2026-07-11 Trade statistics and compare v2

- 使用者截圖確認 V1 在 H4/H1/M30 可顯示 Total、Open、Win TP2、TP1→Loss、Direct Loss、TP1/TP2 Rate、Net R、Avg R、Profit Factor 與訊號漏斗。
- 使用者截圖確認 V2 在 M30 圖表顯示 H4/H1/M30 三列 Compare 表，包含 SETUP、ARMED、Trades、TP1%、TP2%、Net R、Avg R 與 PF；表格位置與 tiny 字體亦有圖表證據。
- 使用者以多個標的取得 H1 730D 統計，證明固定 Window 能讓不同標的採一致期間；這是功能顯示紀錄，不構成策略有效性或勝率驗證。
- Pine Editor 實際回報固定 M30 snapshot 嘗試錯誤：`built-in 'line.set_x2' cannot be used with any parameter of the security() function`；該程式路徑已移除。
- 本次收尾執行 `git diff --check` 與 Pine 靜態內容檢查；結果另記於 commit 前驗證輸出。
- 未實際測試：V2 三列逐筆等同三次 V1、非 M30 圖表固定結果、長時間 Replay 壓力、所有市場/session、最大 arrays/request/object 邊界。因此不標記為通過。

## 2026-07-10 Entry workflow

- 使用者於 TradingView 圖表／Replay 截圖確認過中間版本可顯示 SETUP、ARMED、ENTRY，ARMED 會暗化對應 SETUP，且封存修正後已完成流程的 SETUP 不再因同 zone 新訊號消失。
- Pine Editor 曾回報 box handle 無法使用 `==`；後續版本改用 OB/FVG string key，使用者可再次顯示 ARMED/ENTRY，未再回報該編譯錯誤。
- 本次收尾已執行 Repository 靜態檢查：輸入與物件上限、zone/label/trade-plan 平行 arrays 的 push/shift、zone key 查找與封存、SETUP→ARMED→ENTRY 狀態清除、ENTRY 下一根 K 起算、SL 優先、TP1/TP2/LOSS 狀態，以及非法 SL/TP2 下限防護。
- 本次收尾已執行 `git diff --check`；沒有 whitespace error。
- 尚未驗證：加入最終 Trade Plan 後的 Pine Editor compile、SL/TP 圖表顯示與結果；Daily/H4/M15 完整一致性矩陣；所有取消條件、標籤／物件上限與長時間 Replay 壓力測試。因此這些項目不標記為通過。

## 2026-07-10 Repository 文件整理

- 狀態：完成靜態檢查，未在本次工作中執行 TradingView Pine Editor compile 或圖表視覺驗證。
- 已檢查：文件規格與目前 `smc_weekly_ob_fvg_v1.pine` 的 inputs、Weekly/Daily 聚合、OB/FVG、CHOCH/MSS、midpoint invalidation、物件 trimming 邏輯相符。
- 尚待人工驗證：TradingView Daily、H4、M15 顯示一致性與 Bar Replay 長時間逐 bar 行為。

## 既有測試紀錄摘要

依 `PROJECT_HISTORY.md`，開發期間曾針對 Replay 顯示、物件壓力、OB 重複來源、Daily CHOCH/MSS 跨 timeframe 顯示進行多輪修正。現有 Repository 沒有可自動執行的 Pine 測試套件，也沒有保存足以重現每輪結果的完整測試矩陣，因此不將這些歷史修正標記為全面通過。

## 建議測試矩陣

| 項目 | Daily | H4 | M15 | Replay |
|---|---:|---:|---:|---:|
| Weekly OB/FVG 位置一致 | 待測 | 待測 | 待測 | 待測 |
| Daily CHOCH/MSS 事件一致 | 基準 | 待測 | 待測 | 待測 |
| midpoint invalidation 終止位置 | 待測 | 待測 | 待測 | 待測 |
| 切換 timeframe 後物件保留 | 待測 | 待測 | 待測 | 待測 |
| 達到物件上限時刪除最舊項目 | 待測 | 待測 | 待測 | 待測 |
# V1 / V4 exact-reconciliation follow-up (2026-07-13)

- Previous visual result: 2105 matched every comparable V1/V4 SWING field.
- Previous visual result: 1504 differed by one changed-zone OB SETUP.
- Previous visual result: 2324 differed by five FVG SETUP events.
- Code review found and corrected two non-equivalent rules in V4: pivot tie handling and shared bullish/bearish previous-zone state.
- V4 field names now use the V1 statistics terminology.
- Static checks pass; TradingView compile and the 2105/1504/2324 visual recount remain required because no local Pine compiler is available.

## TradingView visual verification completed

- 2105 / H4 / 1095D: V1 and V4 SWING both report SETUP 30, SETUP replaced 12, ARMED replaced 0, ARMED 1, Total 0, OB SETUP 17, FVG SETUP 13, Same-zone SETUP 17 and Changed-zone SETUP 13.
- 1504 / H4 / 1095D: V1 and V4 SWING both report SETUP 62, SETUP replaced 21, ARMED replaced 0, ARMED 5, Total 4, TP2 Rate 25%, Net R -0.5R, Profit Factor 0.75, OB SETUP 35, FVG SETUP 27, Same-zone SETUP 35 and Changed-zone SETUP 27.
- 2324 / H4 / 1095D: V1 and V4 SWING both report SETUP 45, SETUP replaced 20, ARMED replaced 1, ARMED 1, Total 0, OB SETUP 22, FVG SETUP 23, Same-zone SETUP 27 and Changed-zone SETUP 18.
- Result: all V1-comparable fields match in all three validation symbols. V4-only UNIQUE SETUP, U>A and A>T fields are additional diagnostics and have no V1 counterpart.

## 2026-07-14 per-zone H1 diagnostic

- V1 per-zone build showed Weekly zones and the unsupported table on H4, but rendered nothing on H1; TradingView showed no visible error text.
- Added visible build IDs: `V1-PZ-01` and `V4-PZ-01`.
- V1 now has `Per-zone engine diagnostic`: `OFF` renders without the new engine, `TOUCH` runs zone touch/SETUP creation only, and `FULL` also runs ARMED/ENTRY processing.
- First retest: use H1 with `OFF`. If visible, test `TOUCH`, then `FULL` to isolate the failing stage.
- H1 retest confirmed V1 `V1-PZ-01 / PZ OFF` renders zones and its stats table. Therefore the V1 blank output is inside the per-zone engine, not the base H1/Weekly-zone path.
- 使用者提供的 `2324/H1` 對照截圖確認：V1 `V1-PZ-01 / PZ OFF` 可顯示 Weekly zones 與統計表；切換成 `PZ TOUCH` 後 V1 全部消失，而 V4 `V4-PZ-02 / PZ OFF` 仍正常顯示。這將失敗階段確定縮小到 V1 `TOUCH`。
- 程式定位到 `if fi < 0 or array.get(flowStages, fi) == 1`：V1 使用 Pine v5，`or` 兩側皆會評估；首次遇到新 zone 時 `flowIndex()` 回傳 `-1`，後半段因此讀取負索引並造成 runtime failure。
- `V1-PZ-03` 已改為先判斷 `fi >= 0` 才讀取 `flowStages`。此修改只套用 V1；TradingView 驗證結果如下。
- 使用者完成 `V1-PZ-03` TradingView 對照驗證：`2324/H1 / PZ OFF` 正常顯示；切換為 `PZ TOUCH` 後仍正常顯示 zones、表格與 SETUP labels，SETUP 8、SETUP replaced 3、Same/Changed 3/5、OB/FVG 2/6，ARMED 與 Total 均為 0。
- `1504/H1 / PZ OFF` 正常顯示；切換為 `PZ TOUCH` 後仍正常顯示 zones、表格與 SETUP labels，SETUP 20、SETUP replaced 11、Same/Changed 11/9、OB/FVG 6/14，ARMED 與 Total 均為 0。
- 四張對照截圖均未顯示 runtime 訊息；V4 `V4-PZ-02 / PZ OFF` 同時保持正常。結論：`V1-PZ-03` 的 TOUCH runtime failure 修正通過 2324、1504/H1 實圖驗證；本結論不包含 `FULL`、ARMED 或 ENTRY。
- V4 `V4-PZ-01` rendered its unsupported prompt on H4 but disappeared on H1. Added `V4-PZ-02` with its per-zone engine defaulted to `OFF` so both base tables can first be verified together on H1.
- The attempted `V1-PZ-02 / V4-PZ-03` optimization did not restore H1 `FULL` execution and was rolled back; it must not be treated as a validated fix.

### 2026-07-14 穩定基準驗證

- `1504 / H1`、`2105 / H1`、`2324 / H1`：V1 `V1-PZ-01 / PZ OFF` 與 V4 `V4-PZ-02 / PZ OFF` 均可同時正常顯示。
- `1504 / Daily`：V1 的 Weekly Zone 與 Daily MSS 正常顯示；V1/V4 均顯示應使用 H1 的提示。
- 結論：H1 判斷、Weekly Zone、Daily MSS、表格與兩個指標同時載入均不是根因；問題已縮小到 per-zone SETUP engine。

### 本次除錯教訓

- 不可把靜態 code review 的推測直接當成已確認根因。
- 不可在 V1 尚未單獨通過前，同時修改 V1 與 V4 並預設啟用 `FULL`。
- `OFF` 能顯示只證明基礎路徑正常，不代表 per-zone 功能完成。
- 每次測試必須記錄版號、symbol、timeframe、diagnostic mode 與畫面結果。
- 後續必須依序驗證 V1 `OFF -> TOUCH -> FULL`；V1 穩定後才將相同核心移植到 V4。

### 2026-07-14 正式收尾

- 程式版號確認：V1 `V1-PZ-01`、V4 `V4-PZ-02`。
- 穩定模式確認：兩者均預設 `PZ OFF`；這是可顯示的診斷基準，不代表 per-zone 功能完成。
- Repository 文件已重新分工並修正互相矛盾的完成狀態；所有 Markdown 相對連結檢查通過。
- Repository 靜態檢查 `git diff --check` 通過；本機沒有 Pine compiler，因此沒有新增 TradingView compile 通過宣告。
- 下一個對話只處理 V1 `TOUCH` 驗證，不同步修改 V4。
