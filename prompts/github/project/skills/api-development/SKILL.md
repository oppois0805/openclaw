---
name: api-development
description: "API 三層架構開發：當需要串接後端 API、建立 DTO 介面、撰寫 Core API Service（HTTP 層）、實作 Middleware Service（業務邏輯層）時使用。包含完整的 API 串接流程（DTO → Enum → Core Service → Middleware）、HttpClient 使用規範、Observable 回傳型別定義、三層架構分工原則。適用場景：新增 API 功能、重構 API 呼叫、實作資料存取層。"
---

# API 開發規範

建立 API 串接相關程式碼的完整規範。

## 開發順序

```
1️⃣ 建立 DTO 介面
    ↓
2️⃣ 建立列舉（如需要）
    ↓
3️⃣ 建立 Core API Service
    ↓
4️⃣ 建立 Middleware
```

## 參考規範

執行此技能時需理解以下規範：

| 規範                                                                      | 說明                        |
| :------------------------------------------------------------------------ | :-------------------------- |
| [api-core_service.md](references/api-core_service.md)                     | Core API Service 撰寫範本   |
| [api-middleware_service.md](references/api-middleware_service.md)         | Middleware Service 撰寫範本 |
| [folder-system_api.md](../../rules/folder-system/folder-system_api.md) | API 三層架構目錄配置        |

## Core API Service 重點

- 只負責 HTTP 呼叫
- 不處理業務邏輯
- 使用 `HttpClient` 搭配 Typed Response

## Middleware 重點

- 負責資料轉換、快取、錯誤處理
- 是 Component 與 Core Service 之間的橋樑
- 使用 `inject()` 注入 Core Service

## 禁止事項

- 🔴 在 Core Service 中處理業務邏輯
- 🔴 在 Component 中直接呼叫 Core Service
- 🔴 使用 `any` 作為 API Response 型別
