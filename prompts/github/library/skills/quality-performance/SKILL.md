---
name: performance-check
description: "Angular 效能優化檢查：當需要檢查 Angular 應用效能、優化渲染速度、減少重繪（Reflow）、處理訂閱管理、避免記憶體洩漏時使用。檢查類別：1⃣ 變更偵測優化（OnPush 策略）、2⃣ Template 效能（禁止函式呼叫、@for track 設定）、3⃣ 訂閱與記憶體（takeUntilDestroyed）、4⃣ 資料處理（virtual scroll）。嚴重度：🔴 必須修正、🟡 應盡快修正、🟢 可選採納。適用於效能檢核、渲染優化、記憶體檢查。"
---

# Performance Check

自動檢測 Angular 前端效能問題並提出改進建議。

## 檢查規則

| 類別          | 參考文件                                                                 |
| ------------- | ------------------------------------------------------------------------ |
| 變更偵測優化  | [references/change-detection.md](references/change-detection.md)         |
| Template 效能 | [references/template-performance.md](references/template-performance.md) |
| 訂閱與記憶體  | [references/subscription-memory.md](references/subscription-memory.md)   |
| 資料處理效能  | [references/data-processing.md](references/data-processing.md)           |

## 嚴重程度

| 等級 | 說明                     | 處理       |
| ---- | ------------------------ | ---------- |
| 🔴   | 明顯效能問題或記憶體洩漏 | 必須修正   |
| 🟡   | 可能影響效能             | 應盡快修正 |
| 🟢   | 有更好寫法               | 可選採納   |

## 輸出格式

**有問題時**：

```markdown
# 🔍 效能檢查報告

## 摘要

- 🔴 嚴重：<N> 個
- 🟡 警告：<M> 個

## 🔴 嚴重問題

### 1. <問題標題>

**檔案**：`<path>` line <N>
**問題**：<說明>
**建議修正**：<程式碼>
```

**無問題時**：

```markdown
# 🔍 效能檢查報告

✅ 未發現效能問題。
```

## 常見嚴重問題

- 未使用 `OnPush` 策略
- Template 呼叫 function
- `@for` 未設定 `track`
- 訂閱未用 `takeUntilDestroyed()`
- 大列表無 virtual scroll
