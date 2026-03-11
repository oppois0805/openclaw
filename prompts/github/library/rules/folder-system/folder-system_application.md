# Folder System For Application

此檔案用於應用程式資料夾系統的 Prompt。

# Projects 目錄架構規範

## 📋 總覽

Angular 專案都遵循統一的架構規範，確保程式碼的一致性、可維護性和可擴展性。

## 🏗️ 標準目錄架構

每個子專案都應遵循以下目錄結構 (並非所有 Folder 必須存在，依照功能需求去建立)：

```
├── docs/                       # 用於儲存該功能相關文件 (如需求分析報告)
├── prompt/                     # Prompt 設定檔案資料夾
│   ├── api/                    # API Prompt 設定檔案
│   │   └── {api-name}.md       # 個別 API 的 prompt 設定
│   └── ... (未來可能擴充)      # 其他 Prompt 設定
├── src/app/
│   ├── api/                    # API 讀取服務 (由 API 產生器自動產生)
│   │   └── {service-name}.middleware.ts
│   ├── core/                   # 核心功能
│   │   ├── auth/               # 認證授權相關
│   │   │   ├── guards/         # 認證守衛
│   │   │   ├── interceptors/   # 認證攔截器
│   │   │   ├── models/         # 認證模型
│   │   │   ├── services/       # 認證服務
│   │   │   └── storage/        # 認證儲存
│   │   ├── cache/              # 快取管理
│   │   │   ├── services/       # 快取服務
│   │   │   └── models/         # 快取模型
│   │   ├── config/             # 設定管理
│   │   │   ├── environment/    # 環境設定
│   │   │   ├── constants/      # 設定常數
│   │   │   └── models/         # 設定模型
│   │   ├── constants/          # 全域常數
│   │   ├── enums/              # 全域列舉
│   │   ├── error/              # 錯誤處理
│   │   │   ├── handlers/       # 錯誤處理器
│   │   │   ├── interceptors/   # 錯誤攔截器
│   │   │   └── models/         # 錯誤模型
│   │   ├── guards/             # 路由守衛
│   │   ├── api/                # API 相關模組 (以下皆由 API 產生器自動產生, 勿動)
│   │   │   ├── services/                       # API 服務層
│   │   │   │   ├── {service-name}.service.ts
│   │   │   ├── enum/                           # API 列舉
│   │   │   │   └── {service-name}.enum.ts
│   │   │   └── dto/                            # 後端來源資料模型
│   │   │       └── {service-name}.dto.ts
│   │   ├── models/             # 全域資料模型
│   │   ├── resolvers/          # 元件載入前預先執行
│   │   ├── services/           # 全域服務層 (HTTP 服務除外)
│   │   ├── storage/            # 儲存管理
│   │   │   ├── services/       # 儲存服務
│   │   │   └── models/         # 儲存模型
│   │   ├── utils/              # 工具函式
│   │   │   ├── helpers/        # 輔助函式
│   │   │   ├── formatters/     # 格式化工具
│   │   │   └── validators/     # 驗證工具
│   │   └── validators/         # 全域驗證器
│   ├── features/               # 功能模組
│   │   └── {功能名稱}/          # 具體功能
│   │       ├── features/       # 子功能模組 (遞迴結構)
│   │       │   └── {子功能名稱}/
│   │       │       ├── components/     # 子功能元件 (純 UI 無業務邏輯)
│   │       │       ├── models/         # 子功能模型
│   │       │       ├── testing/                            # 🧪 測試假資料 (推薦)
│   │       │       │  ├─ {model}.mock.ts                 # 📋 模型假資料
│   │       │       │  ├─ {service}.stub.ts               # ⚙️ 服務 stub/fake
│   │       │       │  └─ {helper}.mock.ts                # 🛠️ 工具假資料
│   │       │       ├── api/            # API 讀取服務 (由 API 產生器自動產生)
│   │       │       │   └── {service-name}.middleware.ts
│   │       │       ├── services/       # 子功能服務
│   │       │       ├── guards/         # 子功能守衛
│   │       │       ├── pipes/          # 子功能管道
│   │       │       ├── directives/     # 子功能指令
│   │       │       ├── validators/     # 子功能驗證器
│   │       │       ├── utils/          # 子功能工具
│   │       │       ├── constants/      # 子功能常數
│   │       │       ├── enums/          # 子功能列舉
│   │       │       ├── features/       # 更深層子功能
│   │       │       ├── {子功能名稱}.component.ts
│   │       │       ├── {子功能名稱}.component.html
│   │       │       ├── {子功能名稱}.component.scss
│   │       │       └── {子功能名稱}.component.spec.ts
│   │       ├── components/     # 該功能專用的子元件  (純 UI 無業務邏輯)
│   │       ├── models/         # 功能專用模型
│   │       ├── services/       # 功能專用服務 (業務邏輯)
│   │       │   └── {依照功能需求}.service.ts
│   │       ├── api/            # API 讀取服務 (由 API 產生器自動產生)
│   │       │   └── {service-name}.middleware.ts
│   │       ├── guards/         # 功能專用守衛
│   │       ├── pipes/          # 功能專用管道
│   │       ├── directives/     # 功能專用指令
│   │       ├── validators/     # 功能專用驗證器
│   │       ├── utils/          # 功能專用工具
│   │       ├── constants/      # 功能專用常數
│   │       ├── enums/          # 功能專用列舉
│   │       ├── {功能}.component.ts
│   │       ├── {功能}.component.html
│   │       ├── {功能}.component.scss
│   │       └── {功能}.component.spec.ts
│   ├── shared/                 # 共用元件
│   │   ├── components/         # 共用 UI 元件
│   │   │   ├── buttons/        # 按鈕元件
│   │   │   ├── forms/          # 表單元件
│   │   │   ├── tables/         # 表格元件
│   │   │   ├── charts/         # 圖表元件
│   │   │   ├── dialogs/        # 對話框元件
│   │   │   ├── modals/         # 模態框元件
│   │   │   ├── navigation/     # 導航元件
│   │   │   ├── layout/         # 版面配置元件
│   │   │   ├── feedback/       # 回饋元件（Toast、Alert等）
│   │   │   └── data-display/   # 資料展示元件
│   │   ├── directives/         # 共用指令
│   │   │   ├── validation/     # 驗證指令
│   │   │   ├── ui/             # UI 行為指令
│   │   │   └── utility/        # 工具指令
│   │   ├── pipes/              # 共用管道
│   │   │   ├── format/         # 格式化管道
│   │   │   ├── filter/         # 過濾管道
│   │   │   └── transform/      # 轉換管道
│   │   ├── modules/            # 共用模組
│   │   │   ├── material/       # Material 模組
│   │   │   ├── primeng/        # PrimeNG 模組
│   │   │   └── validators/     # 驗證器模組
│   │   ├── models/             # 共用模型
│   │   ├── services/           # 共用服務 (非 HTTP)
│   │   │   ├── utility/        # 工具服務
│   │   │   ├── notification/   # 通知服務
│   │   │   └── state/          # 狀態管理服務
│   │   ├── utils/              # 共用工具函式
│   │   ├── validators/         # 共用驗證器
│   │   ├── constants/          # 共用常數
│   │   └── enums/              # 共用列舉
│   ├── main.component.ts       # 主要元件
│   ├── main.component.html
│   ├── main.component.scss
│   └── main.component.spec.ts
├── angular.json                # Angular 專案設定
└── tsconfig.json              # TypeScript 設定
```

## 📁 目錄說明

### **Docs（文件目錄）**

#### `docs/` - 功能文件

- **用途**：存放與該專案或功能相關的所有文件
- **內容範例**：
  - 需求分析報告 (`requirements-analysis-*.md`)
  - 技術規格書
  - API 整合文件
  - 使用者手冊
- **結構建議**：
  - 可依照文件類型或功能模組進行子目錄分類
  - 需求分析分析報告預設建議存放於 `docs/analysis/` 下

---

### **Prompt（Prompt 設定目錄）**

#### `prompt/api/` - API 自動產生設定檔案

- **用途**：存放用於自動產生 API 相關程式碼的設定檔案和 Swagger 規格
- **檔案格式**：Markdown (`.md`) 和 JSON (`.json`)
- **主要檔案類型**：

  **1. Swagger 規格檔案**
  - `swagger.json` - OpenAPI/Swagger 格式的 API 完整定義檔案
  - 來源：從後端 API 取得 (例如：`{ip}/api/swagger/v1/swagger.json`)
  - 包含所有 API 端點、參數、回應格式、Schema 定義

  **2. Core Service 配置**
  - `api-core_service.md` - 核心服務生成規範文件
  - `api-core_config.md` - API 列表定義和檔案位置規範
  - 用途：從 swagger.json 自動產生核心 API 服務層

  **3. Shard Service 配置**
  - `api-middleware_service.md` - 封裝服務生成規範文件
  - `api-middleware_config.md` - 核心服務到目標資料夾的映射關係
  - 用途：為核心服務在各功能模組生成封裝中介層

  **4. 使用說明文件**
  - `http-proxy-service.md` - HTTP 請求代理服務使用文檔
  - 包含 API 參考、使用範例、注意事項等

- **自動產生目標**：

  **Core Service 生成目標**（基於 swagger.json）：
  - `src/app/core/api/services/{service-name}.service.ts` - 核心 API 服務層
  - `src/app/core/api/dto/{service-name}.dto.ts` - 資料傳輸物件 (DTO)
  - `src/app/core/api/enum/{service-name}.enum.ts` - API 列舉定義

  **Shard Service 生成目標**（基於 Core Service）：
  - `src/app/api/{service-name}.middleware.ts` - 應用層 API 中介層
  - `src/app/features/{feature-name}/api/{service-name}.middleware.ts` - 功能模組 API 中介層

- **使用流程**：
  ```
  1. 取得 swagger.json 並放入 prompt/api/
  2. 在 api-core_config.md 中定義需要處理的 API 列表
  3. 執行 api-core_service.md 生成核心服務
  4. 在 api-middleware_config.md 中定義映射關係
  5. 執行 api-middleware_service.md 生成封裝服務
  ```

#### `prompt/.../` - 其他 Prompt 設定（未來擴充）

- 保留空間供未來新增其他類型的 Prompt 設定
- 可能包含：
  - 元件產生器配置 (Component Generator)
  - 表單產生器配置 (Form Generator)
  - 測試產生器配置 (Test Generator)
  - 狀態管理產生器配置 (State Generator)

---

### **Core（核心目錄）**

#### `core/auth/` - 認證授權系統

- **`guards/`** - 認證相關的路由守衛（如：登入檢查、權限檢查）
- **`interceptors/`** - 認證相關的 HTTP 攔截器（如：Token 附加、認證失效處理）
- **`models/`** - 認證相關的資料模型（如：User、Token、Permission）
- **`services/`** - 認證相關的服務（如：AuthService、TokenService）
- **`storage/`** - 認證資料的儲存管理（如：TokenStorage、UserStorage）

#### `core/cache/` - 快取管理

- **`services/`** - 快取相關服務（如：CacheService、MemoryCache）
- **`models/`** - 快取相關模型（如：CacheConfig、CacheItem）

#### `core/config/` - 設定管理

- **`environment/`** - 環境相關設定檔案
- **`constants/`** - 設定相關常數
- **`models/`** - 設定相關模型

#### `core/constants/` - 全域常數

- 存放整個應用程式共用的常數定義
- 範例：API 端點、預設值、設定參數

#### `core/enums/` - 全域列舉

- 存放整個應用程式共用的列舉定義
- 範例：狀態碼、類型定義、選項清單
- **適用範圍**：整個子專案內的所有功能模組
- **使用時機**：跨多個功能模組使用的基礎列舉
- **範例**：

  ```typescript
  // core/enums/status.enum.ts
  export enum ApplicationStatus {
    ACTIVE = "active",
    INACTIVE = "inactive",
    PENDING = "pending",
    DELETED = "deleted",
  }

  // core/enums/response-code.enum.ts
  export enum ResponseCode {
    SUCCESS = "200",
    BAD_REQUEST = "400",
    UNAUTHORIZED = "401",
    NOT_FOUND = "404",
  }
  ```

#### `core/error/` - 錯誤處理

- **`handlers/`** - 錯誤處理器（如：GlobalErrorHandler）
- **`interceptors/`** - 錯誤相關攔截器（如：ErrorInterceptor）
- **`models/`** - 錯誤相關模型（如：ErrorResponse、AppError）

#### `core/guards/` - 路由守衛

- 存放應用程式層級的路由守衛
- 範例：`auth.guard.ts`, `admin.guard.ts`, `feature.guard.ts`

#### `core/i18n/` - 國際化

- **`services/`** - 翻譯相關服務（如：TranslateService、LanguageService）
- **`pipes/`** - 翻譯相關管道（如：TranslatePipe）
- **`models/`** - 語言相關模型（如：Language、Translation）

#### `core/interceptors/` - HTTP 攔截器

- 存放 HTTP 攔截器
- 範例：`auth.interceptor.ts`, `error.interceptor.ts`, `loading.interceptor.ts`

#### `core/models/` - 全域資料模型

- 存放共用的資料模型和介面定義
- 包含 API 回應格式、實體模型等
- 範例：
  ```typescript
  export interface ApiResponse<T> {
    code: string | null;
    message: string | null;
    data: T;
    success: boolean;
  }
  ```

#### `core/services/` - HTTP 服務層

- 存放所有 HTTP 服務層程式碼
- 負責與後端 API 的通訊
- 每個服務都應有對應的測試檔案
- 命名規範：依照 Swagger API 標題命名
- 範例：`user-management.service.ts`, `system-config.service.ts`

#### `core/storage/` - 儲存管理

- **`services/`** - 儲存相關服務（如：LocalStorageService、SessionStorageService）
- **`models/`** - 儲存相關模型（如：StorageConfig、StorageItem）

#### `core/utils/` - 工具函式

- **`helpers/`** - 輔助函式（如：dateHelpers、stringHelpers）
- **`formatters/`** - 格式化工具（如：dateFormatter、currencyFormatter）
- **`validators/`** - 驗證工具（如：emailValidator、phoneValidator）

#### `core/validators/` - 全域驗證器

- 存放自訂的表單驗證器
- 範例：`custom-email.validator.ts`, `password-strength.validator.ts`

---

### **Features（功能目錄）**

#### `features/{功能名稱}/` - 具體功能模組

每個功能模組都是一個完整的業務領域，支援巢狀結構：

#### **遞迴結構支援**

- **`features/`** - 子功能模組（支援無限層巢狀）
  - 每個子功能都遵循相同的目錄結構
  - 範例：`user-management/features/profile/features/avatar/`

#### **標準功能結構**

- **`components/`** - 該功能專用的子元件
- **`models/`** - 該功能專用的資料模型
- **`services/`** - 該功能專用的服務（業務邏輯，非 HTTP 服務）
- **`guards/`** - 該功能專用的路由守衛
- **`pipes/`** - 該功能專用的管道
- **`directives/`** - 該功能專用的指令
- **`validators/`** - 該功能專用的驗證器
- **`utils/`** - 該功能專用的工具函式
- **`constants/`** - 該功能專用的常數
- **`enums/`** - 該功能專用的列舉
- **主要元件檔案** - 功能的核心元件

---

### **Shared（共用目錄）**

#### `shared/components/` - 共用 UI 元件

- **`buttons/`** - 按鈕相關元件（如：PrimaryButton、IconButton）
- **`forms/`** - 表單相關元件（如：FormField、FormGroup）
- **`tables/`** - 表格相關元件（如：DataTable、SortableTable）
- **`charts/`** - 圖表相關元件（如：LineChart、BarChart）
- **`dialogs/`** - 對話框相關元件（如：ConfirmDialog、AlertDialog）
- **`modals/`** - 模態框相關元件（如：Modal、Popup）
- **`navigation/`** - 導航相關元件（如：Breadcrumb、Menu）
- **`layout/`** - 版面配置元件（如：Header、Footer、Sidebar）
- **`feedback/`** - 回饋元件（如：Toast、Alert、Loading）
- **`data-display/`** - 資料展示元件（如：Card、List、Badge）

#### `shared/directives/` - 共用指令

- **`validation/`** - 驗證相關指令（如：RequiredValidator、EmailValidator）
- **`ui/`** - UI 行為指令（如：ClickOutside、Tooltip）
- **`utility/`** - 工具指令（如：CopyToClipboard、AutoFocus）

#### `shared/pipes/` - 共用管道

- **`format/`** - 格式化管道（如：DatePipe、CurrencyPipe）
- **`filter/`** - 過濾管道（如：SearchPipe、SortPipe）
- **`transform/`** - 轉換管道（如：CapitalizePipe、TruncatePipe）

#### `shared/modules/` - 共用模組

- **`material/`** - Angular Material 相關模組封裝
- **`primeng/`** - PrimeNG 相關模組封裝
- **`validators/`** - 驗證器模組封裝

#### `shared/models/` - 共用模型

- 存放跨功能使用的資料模型
- 範例：BaseEntity、ListResponse、SelectOption

#### `shared/services/` - 共用服務（非 HTTP）

- **`utility/`** - 工具服務（如：UtilityService、HelperService）
- **`notification/`** - 通知服務（如：ToastService、AlertService）
- **`state/`** - 狀態管理服務（如：StateService、CacheService）

#### `shared/utils/` - 共用工具函式

- 存放跨功能使用的工具函式
- 範例：date utilities、string utilities、array utilities

#### `shared/validators/` - 共用驗證器

- 存放跨功能使用的表單驗證器
- 範例：custom validators、composite validators

#### `shared/constants/` - 共用常數

- 存放跨功能使用的常數定義
- 範例：UI constants、default values

#### `shared/enums/` - 共用列舉

- 存放跨功能使用的列舉定義
- 範例：common status、common types
- **適用範圍**：跨多個功能模組但不涉及核心業務邏輯的列舉
- **使用時機**：UI 相關、展示邏輯相關的列舉定義
- **範例**：

  ```typescript
  // shared/enums/ui-size.enum.ts
  export enum UiSize {
    SMALL = "small",
    MEDIUM = "medium",
    LARGE = "large",
    EXTRA_LARGE = "xl",
  }

  // shared/enums/theme.enum.ts
  export enum Theme {
    LIGHT = "light",
    DARK = "dark",
    AUTO = "auto",
  }

  // shared/enums/sort-direction.enum.ts
  export enum SortDirection {
    ASC = "asc",
    DESC = "desc",
  }
  ```

---

## 🔍 Core vs Shared vs Features 列舉使用差異

### **全域列舉 (Core/enums) vs 共用列舉 (Shared/enums) vs 功能列舉 (Features/enums)**

| 類型         | 位置                     | 使用範圍     | 適用情境               | 範例                                       |
| ------------ | ------------------------ | ------------ | ---------------------- | ------------------------------------------ |
| **全域列舉** | `core/enums/`            | 整個子專案   | 核心業務邏輯、API 相關 | `UserStatus`, `ResponseCode`, `ErrorType`  |
| **共用列舉** | `shared/enums/`          | 跨功能模組   | UI 相關、展示邏輯      | `ButtonSize`, `ThemeMode`, `SortDirection` |
| **功能列舉** | `features/{功能}/enums/` | 單一功能模組 | 特定業務邏輯           | `OrderStatus`, `PaymentMethod`, `UserRole` |

### **選擇指南**

#### **使用 Core/enums 的時機**：

- ✅ 與後端 API 直接對應的列舉
- ✅ 跨多個功能模組的核心業務狀態
- ✅ 系統層級的設定選項
- ✅ 錯誤碼和回應碼

#### **使用 Shared/enums 的時機**：

- ✅ UI 元件的樣式選項
- ✅ 表格排序、篩選選項
- ✅ 主題和視覺相關設定
- ✅ 通用的展示格式選項

#### **使用 Features/enums 的時機**：

- ✅ 特定功能的業務狀態
- ✅ 功能專用的選項清單
- ✅ 與特定功能強相關的類型定義
- ✅ 不會被其他功能使用的列舉

### **實際範例對比**

```typescript
// ❌ 錯誤：把業務邏輯放在 shared
// shared/enums/user-status.enum.ts
export enum UserStatus {
  ACTIVE = "active",
  INACTIVE = "inactive",
}

// ✅ 正確：業務邏輯放在 core
// core/enums/user-status.enum.ts
export enum UserStatus {
  ACTIVE = "active",
  INACTIVE = "inactive",
}

// ✅ 正確：UI 相關放在 shared
// shared/enums/button-variant.enum.ts
export enum ButtonVariant {
  PRIMARY = "primary",
  SECONDARY = "secondary",
  DANGER = "danger",
}

// ✅ 正確：功能專用放在 features
// features/order-management/enums/order-status.enum.ts
export enum OrderStatus {
  DRAFT = "draft",
  SUBMITTED = "submitted",
  PROCESSING = "processing",
  COMPLETED = "completed",
}
```

---

## 💡 實際使用範例

### **範例 1：使用者管理功能**

```
projects/user-management/
├── src/app/main/
│   ├── core/
│   │   ├── models/
│   │   │   ├── user.model.ts           # 使用者基本模型
│   │   │   └── api-response.model.ts   # API 回應格式
│   │   ├── services/
│   │   │   ├── user-api.service.ts     # 使用者 HTTP API 服務
│   │   │   └── user-api.service.spec.ts
│   │   └── guards/
│   │       └── user-admin.guard.ts     # 使用者管理權限守衛
│   ├── features/
│   │   ├── profile/                    # 個人檔案功能
│   │   │   ├── features/
│   │   │   │   ├── avatar/             # 頭像子功能
│   │   │   │   │   ├── components/
│   │   │   │   │   │   └── avatar-crop.component.ts
│   │   │   │   │   ├── services/
│   │   │   │   │   │   └── avatar-upload.service.ts
│   │   │   │   │   └── avatar.component.ts
│   │   │   │   └── settings/           # 設定子功能
│   │   │   │       ├── components/
│   │   │   │       ├── services/
│   │   │   │       └── settings.component.ts
│   │   │   ├── components/
│   │   │   │   └── profile-form.component.ts
│   │   │   ├── services/
│   │   │   │   └── profile-validation.service.ts
│   │   │   └── profile.component.ts
│   │   └── list/                       # 使用者清單功能
│   │       ├── components/
│   │       │   ├── user-filter.component.ts
│   │       │   └── user-table.component.ts
│   │       ├── services/
│   │       │   └── user-list.service.ts
│   │       └── list.component.ts
│   ├── shared/
│   │   ├── components/
│   │   │   ├── forms/
│   │   │   │   ├── user-form-field.component.ts
│   │   │   │   └── password-input.component.ts
│   │   │   └── tables/
│   │   │       └── user-data-table.component.ts
│   │   ├── validators/
│   │   │   ├── username.validator.ts
│   │   │   └── password-strength.validator.ts
│   │   └── enums/
│   │       └── user-role.enum.ts
│   └── main.component.ts
```

### **範例 2：系統設定功能（展示列舉分層使用）**

```
projects/system-config/
├── src/app/main/
│   ├── core/
│   │   ├── enums/                      # 全域列舉
│   │   │   ├── api-status.enum.ts      # API 回應狀態
│   │   │   ├── log-level.enum.ts       # 系統日誌等級
│   │   │   └── config-type.enum.ts     # 設定類型
│   │   ├── models/
│   │   │   ├── config.model.ts
│   │   │   └── setting.model.ts
│   │   ├── services/
│   │   │   ├── config-api.service.ts
│   │   │   └── system-api.service.ts
│   │   └── constants/
│   │       └── config-types.const.ts
│   ├── features/
│   │   ├── network/                    # 網路設定
│   │   │   ├── enums/                  # 功能專用列舉
│   │   │   │   ├── network-protocol.enum.ts    # 網路協定
│   │   │   │   ├── connection-type.enum.ts     # 連線類型
│   │   │   │   └── port-status.enum.ts         # 埠口狀態
│   │   │   ├── features/
│   │   │   │   ├── ip-address/
│   │   │   │   │   └── enums/
│   │   │   │   │       └── ip-version.enum.ts  # IP 版本 (IPv4/IPv6)
│   │   │   │   ├── dns/
│   │   │   │   └── proxy/
│   │   │   ├── components/
│   │   │   ├── services/
│   │   │   └── network.component.ts
│   │   ├── security/                   # 安全設定
│   │   │   ├── enums/                  # 功能專用列舉
│   │   │   │   ├── encryption-type.enum.ts     # 加密類型
│   │   │   │   └── auth-method.enum.ts         # 認證方式
│   │   │   ├── features/
│   │   │   │   ├── firewall/
│   │   │   │   └── certificates/
│   │   │   └── security.component.ts
│   │   └── backup/                     # 備份設定
│   │       ├── enums/                  # 功能專用列舉
│   │       │   ├── backup-frequency.enum.ts    # 備份頻率
│   │       │   └── backup-type.enum.ts         # 備份類型
│   │       ├── services/
│   │       │   ├── backup-schedule.service.ts
│   │       │   └── backup-restore.service.ts
│   │       └── backup.component.ts
│   ├── shared/
│   │   ├── enums/                      # 共用列舉
│   │   │   ├── form-validation.enum.ts # 表單驗證類型
│   │   │   ├── notification-type.enum.ts # 通知類型
│   │   │   ├── modal-size.enum.ts      # 對話框尺寸
│   │   │   └── table-action.enum.ts    # 表格操作類型
│   │   ├── components/
│   │   │   ├── forms/
│   │   │   │   ├── config-form.component.ts
│   │   │   │   └── setting-toggle.component.ts
│   │   │   └── dialogs/
│   │   │       └── confirm-restart.component.ts
│   │   └── utils/
│   │       ├── config-validator.util.ts
│   │       └── format-helper.util.ts
│   └── main.component.ts

# 實際列舉內容範例：

## Core 列舉（全域業務邏輯）
# core/enums/api-status.enum.ts
export enum ApiStatus {
    SUCCESS = 'success',
    ERROR = 'error',
    PENDING = 'pending',
    TIMEOUT = 'timeout'
}

# core/enums/log-level.enum.ts
export enum LogLevel {
    DEBUG = 'debug',
    INFO = 'info',
    WARN = 'warn',
    ERROR = 'error',
    FATAL = 'fatal'
}

## Features 列舉（功能專用業務邏輯）
# features/network/enums/network-protocol.enum.ts
export enum NetworkProtocol {
    TCP = 'tcp',
    UDP = 'udp',
    HTTP = 'http',
    HTTPS = 'https',
    FTP = 'ftp'
}

# features/backup/enums/backup-frequency.enum.ts
export enum BackupFrequency {
    DAILY = 'daily',
    WEEKLY = 'weekly',
    MONTHLY = 'monthly',
    CUSTOM = 'custom'
}

## Shared 列舉（UI 和展示邏輯）
# shared/enums/notification-type.enum.ts
export enum NotificationType {
    SUCCESS = 'success',
    WARNING = 'warning',
    ERROR = 'error',
    INFO = 'info'
}

# shared/enums/modal-size.enum.ts
export enum ModalSize {
    SMALL = 'sm',
    MEDIUM = 'md',
    LARGE = 'lg',
    EXTRA_LARGE = 'xl'
}
```

## 🎯 資料夾選擇指南

### **何時使用 Core？**

- ✅ 跨整個應用程式使用的功能
- ✅ 與外部系統整合的服務
- ✅ 應用程式核心邏輯（認證、錯誤處理）
- ✅ 全域設定和常數
- ✅ **列舉**：API 狀態碼、系統等級設定、核心業務狀態

### **何時使用 Features？**

- ✅ 特定業務功能
- ✅ 可獨立開發和測試的模組
- ✅ 有明確業務邊界的功能
- ✅ 需要支援巢狀子功能的複雜業務
- ✅ **列舉**：功能專用的業務狀態、選項清單、類型定義

### **何時使用 Shared？**

- ✅ 可重複使用的 UI 元件
- ✅ 跨多個功能使用的工具
- ✅ 通用的驗證器和管道
- ✅ 與業務邏輯無關的純技術元件
- ✅ **列舉**：UI 樣式選項、視覺相關設定、表格操作類型

### **列舉使用決策樹**

```
是否與核心業務邏輯相關？
├─ 是 → Core/enums (全域列舉)
│   └─ 範例：UserStatus, ResponseCode, LogLevel
└─ 否 → 是否跨多個功能使用？
    ├─ 是 → Shared/enums (共用列舉)
    │   └─ 範例：ButtonSize, ThemeMode, ModalSize
    └─ 否 → Features/enums (功能列舉)
        └─ 範例：OrderStatus, PaymentMethod, NetworkProtocol
```

### **巢狀 Features 使用時機**

- ✅ 功能包含多個子功能時
- ✅ 需要獨立開發子功能時
- ✅ 子功能有自己的路由時
- ✅ 子功能可能被其他專案重複使用時

## 🚀 最佳實踐建議

### **目錄命名**

- 使用 `kebab-case` 命名法
- 名稱要具描述性且簡潔
- 避免過度巢狀（建議最多 3-4 層）

### **檔案組織**

- 相關檔案放在同一目錄
- 大型功能考慮拆分為子功能

### **依賴關係**

- Core → 不依賴任何其他層
- Features → 可依賴 Core 和 Shared
- Shared → 可依賴 Core，不依賴 Features
