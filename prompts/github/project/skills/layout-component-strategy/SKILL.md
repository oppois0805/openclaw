---
name: component-strategy
description: "元件實作優先級策略：決定使用哪種技術方案實作 UI 元件。General 專案優先級：1⃣ PrimeNG → 2⃣ Tailwind CSS → 3⃣ 純 HTML+SCSS。IWA 專案優先級：1⃣ IWA 元件庫 → 2⃣ PrimeNG → 3⃣ Tailwind CSS → 4⃣ HTML+SCSS。包含 PrimeNG 常用元件對照表、IWA 元件清單、禁止事項。適用於切版前決策、元件選型、技術方案評估。"
---

# Component Strategy

決定前端元件最佳實作策略，優先使用現成元件庫。

---

## General Application 決策流程

```
需要實作 UI 元件
    │
    ▼
1️⃣ PrimeNG 有對應元件？ ─YES→ ✅ 使用 PrimeNG
    │NO
    ▼
2️⃣ Tailwind CSS 可實作？ ─YES→ ✅ 使用 Tailwind
    │NO
    ▼
3️⃣ HTML + SCSS（最後手段）
```

### 禁止事項

- ❌ 已有 PrimeNG 元件（如 DatePicker）卻手刻
- ❌ Tailwind 可實現卻寫 SCSS
- ❌ 同一元件混用 SCSS 與 Tailwind
- ❌ 實作前未查閱 PrimeNG 文件

---

## IWA Application 決策流程

```
需要實作 UI 元件
    │
    ▼
1️⃣ IWA 有對應元件？ ─YES→ ✅ 使用 IWA 元件
    │NO
    ▼
2️⃣ PrimeNG 有對應元件？ ─YES→ ✅ 使用 PrimeNG
    │NO
    ▼
3️⃣ Tailwind CSS 可實作？ ─YES→ ✅ 使用 Tailwind
    │NO
    ▼
4️⃣ HTML + SCSS（最後手段）
```

### IWA 元件庫

| 分類    | 元件                     | 說明         |
| ------- | ------------------------ | ------------ |
| Card    | `iwa-card`               | 基本卡片     |
| Card    | `iwa-card-header`        | 帶標題卡片   |
| Card    | `iwa-card-tab`           | 標籤切換卡片 |
| General | `iwa-dialog-menu`        | 對話框選單   |
| General | `iwa-icon`               | 圖示元件     |
| General | `iwa-stepper`            | 步驟導引     |
| Layout  | `iwa-layout`             | 基本佈局     |
| Layout  | `iwa-layout-header-menu` | 含選單佈局   |
| Mixin   | `iwa-list`               | 進階表格     |

完整 API 參考 `LIBRARY_FILE`

### 次選 PrimeNG

IWA 無對應時使用：`p-tree`、`p-fileupload`、`p-accordion`、`p-progressbar`、`p-skeleton`、`p-toast`

### 禁止事項

- ❌ 已有 IWA 元件（如 iwa-list）卻用 PrimeNG p-table
- ❌ 已有 PrimeNG 元件卻手刻
- ❌ 實作前未查閱 IWA/PrimeNG 文件

---

## 參考規範

執行此技能時需理解以下規範：

| 規範                                                    | 說明                         |
| :------------------------------------------------------ | :--------------------------- |
| [library_doc.md](../../rules/library/library_doc.md) | IWA 元件庫文件（IWA 專案用） |
| https://primeng.org/llms/llms-full.txt                  | PrimeNG 元件文件（線上查詢） |

## PrimeNG 常用元件

| 需求     | 元件           | 需求       | 元件              |
| -------- | -------------- | ---------- | ----------------- |
| 按鈕     | `p-button`     | 日期選擇   | `p-datepicker`    |
| 表格     | `p-table`      | 下拉選單   | `p-select`        |
| 對話框   | `p-dialog`     | 輸入框     | `p-inputtext`     |
| 標籤頁   | `p-tabs`       | 訊息提示   | `p-toast`         |
| 手風琴   | `p-accordion`  | 確認對話框 | `p-confirmdialog` |
| 檔案上傳 | `p-fileupload` | Skeleton   | `p-skeleton`      |
