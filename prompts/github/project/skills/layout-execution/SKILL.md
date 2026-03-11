---
name: layout-execution
description: "切版執行輔助技能：當進行 HTML/SCSS 實際開發、實作 Figma 設計稿、處理元件內部細節時使用。包含：iwa-icon 圖示使用規範（app/lib/icomoon 圖示庫，禁止自創圖示）、Loading 狀態處理（app/shared/component/loading）、i18n 語系整合（禁止硬編碼文字）、Tailwind CSS 應用、HTML/SCSS 最佳實踐。適用於切版執行階段、UI 細節實作、元件樣式調整。"
---

# Layout Execution

執行切版時的輔助規範整合。

## 參考規範

執行此技能時需理解以下規範：

| 規範                                                   | 說明             |
| :----------------------------------------------------- | :--------------- |
| `app/lib/icomoon`                                      | 圖示庫路徑       |
| `app/shared/component/loading`                         | Loading 元件路徑 |
| [global_html.md](../../rules/global/global_html.md) | HTML 撰寫規範    |
| [global_scss.md](../../rules/global/global_scss.md) | SCSS 撰寫規範    |

## 圖示使用

- 優先使用 `iwa-icon` 元件
- 禁止自創圖示名稱
- 找不到圖示時停止詢問使用者

## Loading 使用

- 使用專案統一的 Loading 元件
- 確保載入狀態有適當的視覺回饋

## 語系處理

- 除測試資料外，全使用語系檔
- 參考 `i18n/` 目錄結構
