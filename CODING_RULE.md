# Coding Rules

## Pine Script

- 維持 Pine Script v5，除非另有升級任務。
- 新功能先更新 `SMC_SPEC.md`，確認 timeframe、觸發條件、失效條件與視覺規則後再改程式。
- 僅使用完成的高週期 candle 產生確認訊號，避免 lookahead 或 repainting 路徑。
- Weekly 與 Daily 聚合邏輯須明確區分 current candle 與 completed candle。
- CHOCH、MSS 保持獨立 state，不共用 pivot/trend 造成語意混淆。
- 建立新的 box/line 時，必須同步加入刪除與 array trimming 邏輯。
- 平行 arrays 的 push、shift、set 必須保持相同索引生命週期。
- 不以大量 `label.new` 作為長期 debug 手段。
- 技術識別字與 TradingView/Pine 名詞保留英文；文件敘述使用繁體中文。

## 變更與版本控制

- 固定分工：Codex 直接完成 Pine 檔與 Repository 靜態檢查；TradingView compile、載入與實圖驗證由使用者執行。
- 除非使用者在當次任務明確要求，Codex 不開啟或操作 TradingView。
- Codex 交付時應明確列出待使用者驗證的 symbol、timeframe、設定、觀察欄位與預期判定方式。
- 不把未在 TradingView 驗證的結果寫成「已通過」。
- 只有使用者回報 TradingView 實測結果後，才能把對應證據寫入 `TEST_RESULT.md` 並標記通過或失敗。
- 每次行為變更同步更新 `CHANGELOG.md`、`TEST_RESULT.md` 與必要規格。
- Commit 應聚焦單一目的，訊息使用可追溯的英文動詞描述。
- Push 前檢查 `git diff --check`、完整 diff 與工作區狀態。

## V1／V4 核心對齊

- V1 與 V4 PRIMARY 必須實作同一套策略核心；允許 V1 畫完整物件、V4 只顯示統計，但不允許使用不同的 Zone、Bias、Window、SETUP／ARMED／ENTRY、失效或績效判定。
- 相同 symbol、H1、1095D、參數及資料覆蓋下，兩支程式的所有共通統計欄位必須一致；V4 額外研究欄位不影響 V1 對齊結論。
- 修改流程固定為先完成 V1、由使用者在 TradingView 驗證，再把相同核心移植到 V4；不得在 V1 未通過時同時猜測性修改兩支程式。
- Window 起點、touch state 初始化與 event counting 必須明確採用同一規則。現行規則是不做 Window 前 warm-up，第一根 Window H1 的有效重疊可計為第一筆 touch。
- 優先保持簡單規則與少量核心統計，不增加逐筆診斷、龐大測試矩陣或大量參數；只有實際錯誤定位需要時才加入暫時 diagnostic，驗證後移除或隱藏。

## 策略微調與決策

- 每個策略問題先由 Codex 根據現行 Repository 規格與已知結果完成收斂思考，只提出 2～3 個影響最大的調整建議及理由，再由使用者決定實作項目。
- 不預設展開大量參數組合、測試矩陣或多輪逐步微調；除非使用者明確要求，否則不把所有可能選項都列為測試工作。
- 選定調整後直接同步修改對應版本，使用既定固定案例檢查整體結果與 V1／V4 一致性，不因單一標的或局部數據反覆調參。
- 測試以確認程式正確、避免 regression 並觀察改動後的整體結果為目的，不以反覆搜尋最佳參數為目的，降低 overfitting 風險。

## 對話收尾

使用者要求「收尾」時，必須完成以下工作：

1. 將本對話的重要決策、功能規格與已知問題整理進 Repository。
2. 更新 `CHANGELOG.md`；若有完成或新增的待辦，同步更新 `TODO.md`。
3. 執行必要檢查；只有使用者明確要求時才 commit／push。
4. 提供簡短接手摘要，讓下一個新對話只依 Repository 即可繼續。
5. 若對話使用圖表截圖發現視覺問題或完成實圖驗收，挑選少量關鍵圖片存入 `docs/images/<topic-date>/`，並由 `TEST_RESULT.md` 連結；不保存重複角度或可完全由文字取代的中間截圖。
6. 圖片用來保留視覺脈絡，參數、規則、逐欄數據與通過／未通過結論仍必須以 Markdown 文字記錄，不能只留下圖片。
7. 依 `CLOSEOUT_CHECKLIST.md` 記錄整段對話中的錯誤假設、失敗修改、rollback、證據與學習。
