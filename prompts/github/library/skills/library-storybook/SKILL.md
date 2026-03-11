---
name: storybook
description: "Storybook 展示文件建立：當為 Library 元件建立 Storybook Stories 展示文件時使用。必須建立 5 類 Story：1⃣ AI - Render Test（渲染測試）、2⃣ AI - Interactive Test（互動測試）、3⃣ AI - No Data Test（無資料）、4⃣ AI - Edge Case Test（邊界條件）、5⃣ AI - Test ID Verification（TestID 驗證）。所有 AI 產生 Story 必須在 name 標示 'AI - ' 前綴。包含 Args/Controls 設定、組件說明結構、互動式文件。適用於 Library 元件文件、展示範例、開發測試。"
---

# Storybook 開發

建立符合規範的 Storybook 測試案例。

## 參考規範

執行此技能時**必須**完整理解以下規範：

| 規範                                                                | 說明                   |
| :------------------------------------------------------------------ | :--------------------- |
| [library_storybook.md](../../rules/library/library_storybook.md) | Storybook 撰寫完整規範 |

## Stories 開發順序

```
1️⃣ AI - Render Test（渲染測試）
    ↓
2️⃣ AI - Interactive Test（互動測試）
    ↓
3️⃣ AI - No Data Test（無資料狀態）
    ↓
4️⃣ AI - Edge Case Test（邊界條件）
    ↓
5️⃣ AI - Test ID Verification（TestID 驗證）
```

## AI 標示規則

所有 AI 產生的 Story 必須在 `name` 屬性中標示 `AI - ` 前綴：

```typescript
export const AiRenderTest: Story = {
  name: "AI - Render Test",
  // ...
};
```

## 組件說明結構

Story 的 `parameters.docs.description.component` 必須包含：

- 組件概述
- Import 方式
- 核心介面
- 主要輸入屬性
- 輸出事件
- 列舉定義
- 基本用法
