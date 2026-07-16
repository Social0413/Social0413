# Test Results

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
