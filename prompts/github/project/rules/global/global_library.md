
# IWA Library Angular 20+ 開發規範

> 本文件為 Angular 20+ Library 專案開發規範的索引頁面。
>
> This document serves as an index for Angular 20+ Library development guidelines.

---

## 📚 規範文件索引

### Component 開發

| 文件                                                                                             | 說明                                                |
| ------------------------------------------------------------------------------------------------ | --------------------------------------------------- |
| **[global_component.md](./global_component.md)**                                 | Component 核心規範（Signal、OnPush、Input/Output）  |
| **[global_html.md](./global_html.md)**                                           | HTML Template 規範（Control Flow、track、效能優化） |
| **[global_scss.md](./global_scss.md)**                                           | SCSS 樣式規範（BEM、CSS 變數、響應式設計）          |
| **[state-management_signals.md](../state-management/state-management_signals.md)** | Signal 狀態管理規範（signal、computed、effect）     |
| **[global_typescript.md](./global_typescript.md)**                               | TypeScript 規範（型別定義、命名慣例）               |

### 文件與測試

| 文件                                                                         | 說明                                     |
| ---------------------------------------------------------------------------- | ---------------------------------------- |
| **[library_storybook.md](../library/library_storybook.md)**             | Storybook 規範（Story 撰寫、文件、測試） |
| **[test_component.md](../test/test_component.md)** | Component 測試規範（單元測試、整合測試） |
| **[test_service.md](../test/test_service.md)**     | Service 測試規範（Mock、Stub）           |
| **[test_id.md](../test/test_id.md)**               | 測試 ID 規範（data-testid 命名與使用）   |

### Library 架構

| 文件                                                                                       | 說明                                       |
| ------------------------------------------------------------------------------------------ | ------------------------------------------ |
| **[folder-system_library.md](../folder-system/folder-system_library.md)** | Library 目錄架構規範（元件組織、匯出結構） |

---

## 🏗️ Library Component 架構

```
┌─────────────────────────────────────────────────────────────┐
│                  Library Component                          │
│  (可重用 UI 元件 - 純展示邏輯、無業務邏輯)                    │
└─────────────────────────────────────────────────────────────┘
                              ↓ 提供
┌─────────────────────────────────────────────────────────────┐
│                    Public API                               │
│  (透過 public-api.ts 匯出給外部使用)                         │
└─────────────────────────────────────────────────────────────┘
                              ↓ 文件化
┌─────────────────────────────────────────────────────────────┐
│                     Storybook                               │
│  (元件展示、互動文件、視覺測試)                              │
└─────────────────────────────────────────────────────────────┘
```

---

## 🎯 快速檢查清單

### Component 必須項目

- [ ] 使用 `ChangeDetectionStrategy.OnPush`
- [ ] 使用 `standalone: true`
- [ ] 使用 Signal-based `input()` 和 `output()`
- [ ] 狀態使用 `signal()` 和 `computed()` 管理
- [ ] 無業務邏輯（純 UI 元件）

### HTML Template 必須項目

- [ ] 使用 Control Flow 語法 (`@if`, `@for`, `@switch`)
- [ ] `@for` 迴圈有使用 `track`
- [ ] Template 中沒有直接呼叫 function

### SCSS 必須項目

- [ ] 使用 BEM 命名方式
- [ ] 使用 CSS 變數定義顏色、間距等
- [ ] 巢狀層級不超過 3 層
- [ ] 支援主題客製化

### 測試必須項目

- [ ] 每個公開元件都有對應的 Story
- [ ] 每個元件都有單元測試
- [ ] 使用 `data-testid` 標記可測試元素

---

## 🚫 禁止事項總覽

| 類型           | 禁止項目                                               |
| -------------- | ------------------------------------------------------ |
| **Component**  | `@Input()`、`@Output()` 裝飾器、Constructor 注入       |
| **HTML**       | `*ngIf`、`*ngFor`、`*ngSwitch`、Template function 呼叫 |
| **SCSS**       | `!important`、ID 選擇器、寫死數值、過深巢狀            |
| **Library**    | 包含業務邏輯、直接呼叫 API                             |
| **TypeScript** | 使用 `any`、忽略型別檢查                               |

---

## 📌 Library vs Application 差異

| 特性     | Library               | Application           |
| -------- | --------------------- | --------------------- |
| 業務邏輯 | ❌ 無                 | ✅ 有                 |
| API 呼叫 | ❌ 無                 | ✅ 有                 |
| 狀態管理 | 僅 Component 內部狀態 | 跨 Component 狀態管理 |
| 匯出方式 | `public-api.ts`       | 無需匯出              |
| 文件     | Storybook 必須        | 可選                  |

