# IWA Angular 20+ 開發規範

> 本文件為 Angular 20+ 專案開發規範的索引頁面。
>
> This document serves as an index for Angular 20+ development guidelines.

---

## 📚 規範文件索引

### Component 開發

| 文件                                                                               | 說明                                                |
| ---------------------------------------------------------------------------------- | --------------------------------------------------- |
| **[global_component.md](./global_component.md)**                                   | Component 核心規範（Signal、OnPush、Input/Output）  |
| **[global_html.md](./global_html.md)**                                             | HTML Template 規範（Control Flow、track、效能優化） |
| **[global_scss.md](./global_scss.md)**                                             | SCSS 樣式規範（BEM、CSS 變數、響應式設計）          |
| **[state-management_signals.md](../state-management/state-management_signals.md)** | Signal 狀態管理規範（signal、computed、effect）     |
| **[global_typescript.md](./global_typescript.md)**                                 | TypeScript 規範（型別定義、命名慣例）               |

### Service 開發

| 文件                                                              | 說明                                                                 |
| ----------------------------------------------------------------- | -------------------------------------------------------------------- |
| **[agent_service.md](../agent/agent_service.md)**                 | Service 業務邏輯層規範（從 Component 抽離業務邏輯，呼叫 Middleware） |
| **[folder-system_api.md](../folder-system/folder-system_api.md)** | API 呼叫規範（Core API Service、Middleware、HttpProxyService）       |

### 測試規範

| 文件                                               | 說明                                     |
| -------------------------------------------------- | ---------------------------------------- |
| **[test_component.md](../test/test_component.md)** | Component 測試規範（單元測試、整合測試） |
| **[test_service.md](../test/test_service.md)**     | Service 測試規範（Mock、Stub）           |
| **[test_id.md](../test/test_id.md)**               | 測試 ID 規範（data-testid 命名與使用）   |

### 專案架構

| 文件                                                                              | 說明                                                          |
| --------------------------------------------------------------------------------- | ------------------------------------------------------------- |
| **[folder-system_application.md](../folder-system/folder-system_application.md)** | 專案目錄架構規範（Core、Features、Shared 目錄結構與使用時機） |

---

## 🏗️ 四層架構概述

```
┌─────────────────────────────────────────────────────────────┐
│                    Component Layer                          │
│  (UI 元件 - 僅處理視圖邏輯、使用者互動)                        │
└─────────────────────────────────────────────────────────────┘
                              ↓ 呼叫
┌─────────────────────────────────────────────────────────────┐
│                    Service Layer                            │
│  (業務邏輯層 - 處理複雜業務邏輯、狀態管理)                    │
└─────────────────────────────────────────────────────────────┘
                              ↓ 呼叫
┌─────────────────────────────────────────────────────────────┐
│                   Middleware Layer                          │
│  (API 封裝層 - 處理資料轉換、錯誤處理)                        │
└─────────────────────────────────────────────────────────────┘
                              ↓ 呼叫
┌─────────────────────────────────────────────────────────────┐
│                 Core API Service Layer                      │
│  (底層 HTTP 呼叫 - 使用 HttpProxyService)                    │
└─────────────────────────────────────────────────────────────┘
```

---

## 🎯 快速檢查清單

### Component 必須項目

- [ ] 使用 `ChangeDetectionStrategy.OnPush`
- [ ] 使用 `standalone: true`
- [ ] 使用 Signal-based `input()` 和 `output()`
- [ ] 狀態使用 `signal()` 和 `computed()` 管理

### HTML Template 必須項目

- [ ] 使用 Control Flow 語法 (`@if`, `@for`, `@switch`)
- [ ] `@for` 迴圈有使用 `track`
- [ ] Template 中沒有直接呼叫 function

### SCSS 必須項目

- [ ] 使用 BEM 命名方式
- [ ] 使用 CSS 變數定義顏色、間距等
- [ ] 巢狀層級不超過 3 層

### Service / API 必須項目

- [ ] 複雜業務邏輯抽離到 Service 層
- [ ] API 呼叫透過 Middleware 進行
- [ ] 在 `ngOnDestroy` 中呼叫 Middleware 的 `destroy()`

### 測試必須項目

- [ ] 每個 Component 都有單元測試
- [ ] 使用 `data-testid` 標記可測試元素
- [ ] Service 測試使用適當的 Mock/Stub

---

## 🚫 禁止事項總覽

| 類型           | 禁止項目                                               |
| -------------- | ------------------------------------------------------ |
| **Component**  | `@Input()`、`@Output()` 裝飾器、Constructor 注入       |
| **HTML**       | `*ngIf`、`*ngFor`、`*ngSwitch`、Template function 呼叫 |
| **SCSS**       | `!important`、ID 選擇器、寫死數值、過深巢狀            |
| **Service**    | 直接在 Component 寫複雜業務邏輯                        |
| **API**        | 直接在 Component 呼叫 Core API Service                 |
| **TypeScript** | 使用 `any`、忽略型別檢查                               |
