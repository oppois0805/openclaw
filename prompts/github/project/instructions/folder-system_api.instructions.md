# IWA API 串接開發規範

> 本文件規範 Feature 開發中 API 串接的檔案產生邏輯與架構，確保程式碼的可維護性與一致性。

---

## 📁 目錄結構規範

### 三層架構目錄配置

```
app/
├── core/
│   ├── api/
│   │   ├── dto/
│   │   │   └── {resource-name}.dto.ts      # DTO 介面定義（全域共用）
│   │   └── services/
│   │       └── {resource-name}.service.ts  # Core API Service（全域共用）
│   └── enums/
│       └── {resource-name}.ts              # 相關列舉定義（全域共用）
│
└── projects/
    └── {feature-name}/
        └── src/app/main/
            ├── api/                                    # Feature 內共用的 Middleware
            │   └── {resource-name}.middleware.ts
            ├── core/
            │   └── api/
            │       ├── dto/
            │       │   └── {resource-name}.dto.ts      # Feature 專用 DTO
            │       └── services/
            │           └── {resource-name}.service.ts  # Feature 專用 Service
            └── features/
                └── {sub-feature}/
                    ├── api/                            # 與 Component 同層的 Middleware
                    │   └── {resource-name}.middleware.ts
                    ├── {sub-feature}.component.ts
                    ├── {sub-feature}.component.html
                    └── {sub-feature}.component.scss
```

### 放置原則

| 使用範圍           | DTO & Service 位置                          | Middleware 位置                                                     |
| ------------------ | ------------------------------------------- | ------------------------------------------------------------------- |
| **全域共用**       | `app/core/api/`                             | （不適用 - Middleware 不應全域共用）                                |
| **Feature 內**     | `projects/{feature}/src/app/main/core/api/` | （不適用 - 應在 Component 層）                                      |
| **單一 Component** | `projects/{feature}/src/app/main/core/api/` | `projects/{feature}/src/app/main/features/{component-name}/api/` ✅ |

**重要規則**：

1. ✅ **每個 Middleware 獨立服務單一 Component**（一對一關係）
2. ✅ Middleware **必須**放在使用它的 Component 同一層的 `api/` 目錄中
3. ✅ 不同 Component 即使使用相同的 Core API Service，也應建立各自的 Middleware
4. ✅ Core API Service 和 DTO 可以跨 Component 共用
5. ❌ **不應該**有多個 Component 共用同一個 Middleware

---

## 🏗️ 三層架構說明

```
┌─────────────────────────────────────────────────────────────┐
│                    Component Layer                          │
│  (UI 元件 - 僅處理視圖邏輯、使用者互動)                        │
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

### 各層職責

| 層級                 | 職責                              | 檔案命名                        |
| -------------------- | --------------------------------- | ------------------------------- |
| **DTO**              | 定義 Request/Response 介面        | `{resource-name}.dto.ts`        |
| **Enum**             | 定義相關列舉常數                  | `{resource-name}.ts`            |
| **Core API Service** | 定義 API 路徑與呼叫方法           | `{resource-name}.service.ts`    |
| **Middleware**       | 封裝 Core API、資料轉換、錯誤處理 | `{resource-name}.middleware.ts` |

---

## 📝 檔案產生流程

### 開發順序

```
Step 1: 建立 DTO 介面
    ↓
Step 2: 建立列舉（如需要）
    ↓
Step 3: 建立 Core API Service
    ↓
Step 4: 建立 Middleware
```

### Step 1: 建立 DTO 介面

**檔案位置**: `core/api/dto/{resource-name}.dto.ts`

**產生內容**:

- Response 介面（API 回傳資料結構）
- Request 介面（API 請求資料結構）
- Filter 介面（查詢篩選條件）

**命名規範**:

- Response: `{ResourceName}Response`
- Create Request: `Create{ResourceName}Request`
- Update Request: `Update{ResourceName}Request`
- Filter: `{ResourceName}FilterRequest`

### Step 2: 建立列舉（如需要）

**檔案位置**: `core/enums/{resource-name}.ts`

**產生內容**:

- 狀態列舉
- 類型列舉
- 其他常數列舉

**命名規範**:

- `{ResourceName}Status`
- `{ResourceName}Type`

### Step 3: 建立 Core API Service

**檔案位置**: `core/api/services/{resource-name}.service.ts`

**產生內容**:

- API 路徑定義（private readonly）
- CRUD 方法（回傳 Observable）

**命名規範**:

- Service: `{ResourceName}Service`
- 方法: `get{ResourceName}`, `create{ResourceName}`, `update{ResourceName}`, `delete{ResourceName}`

### Step 4: 建立 Middleware

**檔案位置規則**:

**🎯 核心原則**：

1. **每個 Middleware 獨立服務單一 Component**（一對一關係）
2. **必須**建立在正在開發的 Component 同一層的 `api/` 資料夾下
3. **不共用**：不同 Component 即使使用相同的 Core API Service，也應建立各自的 Middleware

#### 標準目錄結構範例

```
app/
├── core/
│   ├── api/
│   │   ├── dto/
│   │   │   └── {resource-name}.dto.ts      # DTO 介面定義（可共用）
│   │   └── services/
│   │       └── {resource-name}.service.ts  # Core API Service（可共用）
│   └── enums/
│       └── {resource-name}.ts              # 相關列舉定義（可共用）
│
└── projects/
    └── {feature-name}/
        └── src/app/main/
            ├── core/
            │   └── api/
            │       ├── dto/
            │       │   └── {resource-name}.dto.ts      # Feature 專用 DTO
            │       └── services/
            │           └── {resource-name}.service.ts  # Feature 專用 Service
            └── features/
                ├── component-a/
                │   ├── api/                            # Component A 專用 Middleware
                │   │   └── {resource-name}.middleware.ts
                │   ├── component-a.component.ts
                │   ├── component-a.component.html
                │   └── component-a.component.scss
                └── component-b/
                    ├── api/                            # Component B 專用 Middleware
                    │   └── {resource-name}.middleware.ts
                    ├── component-b.component.ts
                    ├── component-b.component.html
                    └── component-b.component.scss
```

#### 放置規則說明

**預設規則**：每個 Component 都有自己獨立的 Middleware

- **放置位置**：**永遠**與 Component 同層的 `api/` 目錄
- **命名規範**：根據 Component 的業務邏輯需求命名（不一定與 Core API Service 同名）
- **適用情況**：所有情況（這是標準做法）

**實際範例**：

```
範例 1：列表頁和詳細頁使用相同的 Core API Service
features/
├── brand-list/
│   ├── api/
│   │   └── brand-list.middleware.ts        # 列表頁專用（處理列表邏輯）
│   └── brand-list.component.ts
└── brand-detail/
    ├── api/
    │   └── brand-detail.middleware.ts      # 詳細頁專用（處理詳細邏輯）
    └── brand-detail.component.ts

→ 兩個 Component 都呼叫同一個 BrandService
→ 但各自有獨立的 Middleware 處理不同的資料轉換邏輯

範例 2：巢狀功能結構
features/user/
├── profile/
│   ├── api/
│   │   └── user-profile.middleware.ts      # Profile Component 專用
│   ├── profile.component.ts
│   └── features/
│       ├── avatar/
│       │   ├── api/
│       │   │   └── user-avatar.middleware.ts   # Avatar Component 專用
│       │   └── avatar.component.ts
│       └── settings/
│           ├── api/
│           │   └── user-settings.middleware.ts # Settings Component 專用
│           └── settings.component.ts
```

#### 為什麼每個 Component 需要獨立的 Middleware？

**優點**：

1. ✅ **職責分離**：每個 Middleware 只處理該 Component 的業務邏輯
2. ✅ **易於維護**：修改不會影響其他 Component
3. ✅ **獨立測試**：可以針對單一 Component 進行測試
4. ✅ **資料轉換獨立**：不同 Component 可能需要不同的資料格式
5. ✅ **避免耦合**：Component 之間不會因為共用 Middleware 而產生依賴關係

**記住**：

- Core API Service 可以共用（負責 HTTP 呼叫）
- DTO 可以共用（定義資料結構）
- **Middleware 不共用**（負責業務邏輯封裝）

**產生內容**:

- 封裝 Core API Service 的方法
- 資料轉換邏輯
- 錯誤處理邏輯
- `destroy()` 方法

**命名規範**:

- Service: `{ResourceName}MiddlewareService`
- 方法: 與 Core API Service 對應，但回傳 Promise

---

## 📋 方法回傳值規範

| 層級                 | 方法類型                | 回傳值                                                       |
| -------------------- | ----------------------- | ------------------------------------------------------------ |
| **Core API Service** | 所有方法                | `Observable<ApiResult<T>>` 或 `Observable<ApiListResult<T>>` |
| **Middleware**       | 查詢（陣列）            | `Promise<T[]>`                                               |
| **Middleware**       | 查詢（單一）            | `Promise<T \| null>`                                         |
| **Middleware**       | 查詢（布林）            | `Promise<boolean>`                                           |
| **Middleware**       | 異動（POST/PUT/DELETE） | `Promise<ApiResult<T>>`                                      |

---

## ✅ 開發檢查清單

### DTO 檢查項目

- [ ] 所有欄位都有 JSDoc 註解
- [ ] Response 和 Request 介面分開定義
- [ ] 使用適當的型別（避免 `any`）

### Core API Service 檢查項目

- [ ] API 路徑使用 private readonly 定義
- [ ] 回傳型別為 `Observable<ApiResult<T>>` 或 `Observable<ApiListResult<T>>`
- [ ] 使用 `inject()` 函式進行相依性注入

### Middleware 檢查項目

- [ ] 實作 `destroy()` 方法
- [ ] 查詢方法返回 `Promise<T[]>` 或 `Promise<T | null>`
- [ ] 異動方法返回 `Promise<ApiResult<T>>`
- [ ] 生成唯一的 `_serviceUuid`

### Component 使用檢查項目

- [ ] 在 `ngOnDestroy` 中呼叫 `_middleware.destroy()`
- [ ] 使用 async/await 呼叫 Middleware 方法

---

## 🚫 禁止事項

- ❌ 不直接在 Component 中呼叫 Core API Service（必須透過 Middleware）
- ❌ 不使用 Constructor 注入（必須使用 `inject()` 函式）
- ❌ 不在 Middleware 中遺漏 `destroy()` 方法
- ❌ 不在 Component 中遺漏呼叫 `_middleware.destroy()`

---

## 📚 實際範例：維護狀態檢查

以下是一個實際的 API 串接範例，展示完整的三層架構檔案結構：

### 需求說明

當 HTTP 請求發生 404/500/502/503 錯誤時，呼叫 `log/getLogByType/postUpload` 檢查伺服器是否處於維護模式。

### 產生檔案清單

```
app/
├── api/
│   └── maintenance-status.middleware.ts    # Step 4: Middleware
├── core/
│   ├── api/
│   │   ├── dto/
│   │   │   └── maintenance-status.dto.ts   # Step 1: DTO
│   │   └── services/
│   │       └── maintenance-status.service.ts # Step 3: Core API Service
│   └── enums/
│       └── maintenance-status.ts           # Step 2: Enum
```

### 各檔案內容說明

| 檔案                               | 產生內容                                                             |
| ---------------------------------- | -------------------------------------------------------------------- |
| `maintenance-status.dto.ts`        | `MaintenanceStatusResponse` 介面                                     |
| `maintenance-status.ts`            | `MaintenanceStatus` 列舉（Maintenance, Running）                     |
| `maintenance-status.service.ts`    | `getMaintenanceStatus()` 方法                                        |
| `maintenance-status.middleware.ts` | `checkMaintenanceStatus()`, `getMaintenanceInfo()`, `destroy()` 方法 |

### 運作流程

```
HTTP 錯誤 (404/500/502/503)
        ↓
Interceptor 呼叫 Middleware.checkMaintenanceStatus()
        ↓
Middleware 呼叫 CoreService.getMaintenanceStatus()
        ↓
Core API Service 呼叫 log/getLogByType/postUpload
        ↓
┌─────────────────────────────────┐
│ status === 'maintenance'        │ → 導航到 /
│ status !== 'maintenance'        │ → 顯示錯誤或拋出原始錯誤
└─────────────────────────────────┘
```

---

## 📖 相關文件

- [api-core_config.md](../api/api-core_config.md) - Core API Service 配置
- [api-core_service.md](../api/api-core_service.md) - Core API Service 自動生成
- [api-middleware_config.md](../api/api-middleware_config.md) - Middleware 配置
- [api-middleware_service.md](../api/api-middleware_service.md) - Middleware 自動生成
- [http-proxy-service.md](../api/http-proxy-service.md) - HttpProxyService 詳細說明
