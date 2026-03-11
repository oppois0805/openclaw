---
name: responsive-layout
description: "響應式排版設計技能：當需要實作響應式佈局、處理多裝置適配（Desktop/Tablet/Mobile）、從 Figma 轉換 RWD 設計時使用。核心原則：禁止固定 width/height（如 width:320px），必須使用 Flexbox/Grid + 百分比/fr 單位。包含：斷點定義、Tailwind 響應式工具類、網格系統、Mobile First 策略、Figma 轉 Code 規則。適用於 RWD 實作、自適應排版、多裝置相容。"
---

# Responsive Layout

從 Figma 或設計圖進行切版時的自適應排版規則。

## 核心規則

### 禁止

| 禁止項目             | 替代方案                     |
| -------------------- | ---------------------------- |
| `width: 320px`       | `width: 100%` 或 `max-width` |
| `height: 500px`      | `min-height` 或 `flex: 1`    |
| `margin-left: 150px` | `gap`、百分比                |

### 必須

- 優先使用 Flexbox/Grid
- 寬度使用百分比或 fr 單位
- 高度依內容自適應
- 間距使用相對單位（`rem`、`%`）

## 詳細規則

| 文件                                                       | 說明               |
| ---------------------------------------------------------- | ------------------ |
| [references/flexbox-grid.md](references/flexbox-grid.md)   | Flexbox/Grid 佈局  |
| [references/sizing-units.md](references/sizing-units.md)   | 尺寸與單位規則     |
| [references/figma-to-code.md](references/figma-to-code.md) | Figma 設計轉換規則 |

## 完成檢查

- [ ] 無固定 `width`（除非有 `max-width` 搭配）
- [ ] 無固定 `height`（除非是 `min-height`）
- [ ] 所有容器使用 Flexbox 或 Grid
- [ ] 間距使用 `gap` 或相對單位
