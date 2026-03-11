---
name: api-spec-analysis
description: "API 規格分析與 DTO 產生：當需要從 Swagger/OpenAPI 文件、後端 API 規格書、或口頭需求中產生 TypeScript DTO 介面和 Enum 定義時使用。支援兩種模式：Strict Mode（有 Swagger，嚴格對應 Schema）、Interactive Mode（無 Swagger，互動詢問確認）。會處理型別對映、enum 產生、欄位必填分析、分頁需求識別。適用於 API 串接前置作業、規格轉換、介面定義階段。"
---

# API Spec Analysis

分析 API 規格來源，產出標準化的 DTO 和 Enum 定義。

## 分析流程

### 1. 判斷來源

```
有 Swagger/OpenAPI？
    ├─ YES → Strict Mode（嚴格對應）
    └─ NO  → Interactive Mode（詢問確認）
```

### 2. 依模式處理

**Strict Mode（有 Swagger）**

- 嚴格對應 Schema，禁止自創欄位
- 從 `enum` 屬性產生 TypeScript enum
- 型別對映：`integer` → `number`、`string(date)` → `string`

**Interactive Mode（無 Swagger）**

- 詢問：功能目的、欄位定義、分頁需求、狀態列舉
- 設計 DTO 後必須向使用者確認

## 輸出產物

1. **DTO Interface** (`.dto.ts`)
2. **Enum Definition** (`.enum.ts`)

## 錯誤處理

- Swagger 文件不可讀 → 停止並回報
- 手動需求不清楚 → 停止並詢問細節
