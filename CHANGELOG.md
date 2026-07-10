# Changelog

本文件依現有 Git history 與 `PROJECT_HISTORY.md` 整理；早期版本未使用正式 release tag。

## Unreleased

- 新增 `SMC_SPEC.md`、`DESIGN.md`、`ROADMAP.md`、`CODING_RULE.md`、`TEST_RESULT.md`、`KNOWN_BUGS.md`、`TODO.md`，並建立 Repository 知識索引。

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
