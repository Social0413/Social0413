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

- 不把未在 TradingView 驗證的結果寫成「已通過」。
- 每次行為變更同步更新 `CHANGELOG.md`、`TEST_RESULT.md` 與必要規格。
- Commit 應聚焦單一目的，訊息使用可追溯的英文動詞描述。
- Push 前檢查 `git diff --check`、完整 diff 與工作區狀態。

## 策略微調與決策

- 每個策略問題先由 Codex 根據現行 Repository 規格與已知結果完成收斂思考，只提出 2～3 個影響最大的調整建議及理由，再由使用者決定實作項目。
- 不預設展開大量參數組合、測試矩陣或多輪逐步微調；除非使用者明確要求，否則不把所有可能選項都列為測試工作。
- 選定調整後直接同步修改對應版本，使用既定固定案例檢查整體結果與 V1／V4 一致性，不因單一標的或局部數據反覆調參。
- 測試以確認程式正確、避免 regression 並觀察改動後的整體結果為目的，不以反覆搜尋最佳參數為目的，降低 overfitting 風險。

## 對話收尾

使用者要求「收尾」時，必須完成以下工作：

1. 將本對話的重要決策、功能規格與已知問題整理進 Repository。
2. 更新 `CHANGELOG.md`；若有完成或新增的待辦，同步更新 `TODO.md`。
3. 執行必要檢查後 commit，並 push 到 GitHub。
4. 提供簡短接手摘要，讓下一個新對話只依 Repository 即可繼續。
5. 若對話使用圖表截圖發現視覺問題或完成實圖驗收，挑選少量關鍵圖片存入 `docs/images/<topic-date>/`，並由 `TEST_RESULT.md` 連結；不保存重複角度或可完全由文字取代的中間截圖。
6. 圖片用來保留視覺脈絡，參數、規則、逐欄數據與通過／未通過結論仍必須以 Markdown 文字記錄，不能只留下圖片。
