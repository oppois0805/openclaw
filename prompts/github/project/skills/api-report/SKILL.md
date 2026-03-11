---
name: api-development-report
description: "API 開發完成回報：當完成 API 三層架構開發（DTO/Enum/Core Service/Middleware）後，產生標準化開發報告時使用。會自動檢查：DTO 注釋完整性、型別正確性、Observable 回傳格式、Middleware destroy() 實作、編譯錯誤。輸出包含：檔案清單、介面定義、API 方法簽章、回傳型別、問題清單。適用於 API 開發任務結束階段、Code Review 前準備、交付文件產生。"
---

# API 開發回報

API 開發任務完成後的標準化回報格式。

## 回報前檢查

- [ ] DTO 欄位都有 JSDoc 註解
- [ ] Response/Request 介面分開定義
- [ ] 使用適當型別（避免 `any`）
- [ ] Enum 獨立檔案
- [ ] Core Service 使用 `inject()` 與 `Observable<ApiResult<T>>`
- [ ] Middleware 實作 `destroy()` 與 `_serviceUuid`
- [ ] 無 TypeScript 編譯錯誤

## 輸出模板

```markdown
## API 開發完成報告

### 📁 產生檔案清單

#### DTO 介面

- `{path}/dto/{resource}.dto.ts`
  - `{Resource}Response`
  - `Create{Resource}Request`

#### 列舉

- `{path}/enums/{resource}.ts`
  - `{Resource}Status`

#### Core API Service

- `{path}/services/{resource}.service.ts`
  - `get{Resource}()` → `Observable<ApiResult<T>>`
  - `get{Resource}List()` → `Observable<ApiListResult<T>>`

#### Middleware

- `{path}/api/{resource}.middleware.ts`
  - `get{Resource}()` → `Promise<T | null>`
  - `destroy()` - 清理方法

### ✅ 開發檢查

- [x] DTO 註解完整
- [x] Core Service 回傳型別正確
- [x] Middleware 實作 destroy()

### ⚠️ 問題回報

{issues or "無問題"}
```

## 回傳型別參考

| 層級         | 方法類型     | 回傳值                     |
| ------------ | ------------ | -------------------------- |
| Core Service | 所有         | `Observable<ApiResult<T>>` |
| Middleware   | 查詢（陣列） | `Promise<T[]>`             |
| Middleware   | 查詢（單一） | `Promise<T \| null>`       |
| Middleware   | 異動         | `Promise<ApiResult<T>>`    |
