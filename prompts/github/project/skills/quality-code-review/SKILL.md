---
name: code-review
description: "程式碼審查與報告產生：當完成程式碼開發後，執行全面的前端代碼審查並產生標準化報告時使用。檢查範圍：代碼品質（命名、結構、TypeScript 規範）、效能優化（OnPush、takeUntilDestroyed、禁止 HTML 函式呼叫）、安全防護、使用者體驗、架構設計。問題分類：🚨 嚴重（必須修復）、⚡ 效能（建議修復）、🔧 品質（可選優化）、📋 架構（規劃處理）。適用於 Code Review、品質檢核、交付前檢查。"
---

# Code Review

執行前端程式碼審查並產生標準化報告。

## 參考規範

執行此技能時**必須**完整理解以下規範：

| 規範                                                                      | 說明                   |
| :------------------------------------------------------------------------ | :--------------------- |
| [code-review_report.md](../../rules/code-review/code-review_report.md) | 審查報告格式與檢查清單 |
| [global_html.md](../../rules/global/global_html.md)                    | HTML 撰寫規範          |
| [global_scss.md](../../rules/global/global_scss.md)                    | SCSS 撰寫規範          |
| [global_typescript.md](../../rules/global/global_typescript.md)        | TypeScript 撰寫規範    |
| [global_component.md](../../rules/global/global_component.md)          | Component 開發規範     |

## 審查流程

```
1️⃣ 檢查程式碼品質
    ↓
2️⃣ 檢查效能問題
    ↓
3️⃣ 檢查安全防護
    ↓
4️⃣ 產生審查報告
    ↓
5️⃣ 立即修正問題（如有）
```

## 問題嚴重程度

| 等級    | 說明                   | 處理     |
| :------ | :--------------------- | :------- |
| 🚨 嚴重 | 安全漏洞、核心功能錯誤 | 必須修復 |
| ⚡ 效能 | 影響效能的問題         | 建議修復 |
| 🔧 品質 | 可讀性、命名規範       | 可選優化 |
| 📝 架構 | 架構改善建議           | 規劃處理 |

## 重點檢查項目

- [ ] `*.html` 符合 HTML 規範
- [ ] `*.scss` 符合 SCSS 規範
- [ ] `*.ts` 符合 TypeScript 規範
- [ ] 使用 `ChangeDetectionStrategy.OnPush`
- [ ] 禁止在 HTML 中呼叫函式
- [ ] 訂閱使用 `takeUntilDestroyed()`
- [ ] PrimeNG 元件使用正確
