---
name: frontend-layout-report
description: "切版完成報告技能：當完成 HTML/SCSS/Component 開發後，產生標準化切版完成報告時使用。回報前會檢查：是否使用所有可用 IWA/PrimeNG 元件、純 HTML/SCSS 是否有充分理由、Test ID 規範、TypeScript 編譯狀態。報告包含：檔案清單、UI 元件使用清單（IWA/PrimeNG/純HTML）、元件選擇決策說明、問題回報。適用於切版任務結束、交付文件、Code Review 前準備。"
---

# 前端切版回報

## 回報前檢查

- [ ] 已使用所有可用的 IWA/PrimeNG 元件
- [ ] 純 HTML/SCSS 元素有充分理由
- [ ] 無 TypeScript 編譯錯誤
- [ ] Test ID 依規範產生

## 輸出模板

```markdown
## 切版完成報告

### 📁 檔案清單

- {component-name}.component.ts
- {component-name}.component.html
- {component-name}.component.scss

### 🎨 元件使用清單

#### IWA 元件

- ✅ `{selector}` - {usage}

#### PrimeNG 元件

- ✅ `{selector}` - {usage}

#### 純 HTML + SCSS

- ⚠️ {element} - {justification}

### 📋 元件選擇決策說明

{brief explanation}

### ⚠️ 問題回報

{issues or "無問題"}
```
