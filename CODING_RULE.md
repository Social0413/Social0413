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
