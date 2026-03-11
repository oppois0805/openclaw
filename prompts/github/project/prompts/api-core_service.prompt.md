---
name: "api-core_service"
description: "Core API 服務提示：用於產生 Core 層級的 API Service 程式碼"
---

# 你的任務

本開發計劃提供標準化的 API 自動開發流程，根據 `swagger.json` 檔案和指定的 API 列表自動生成 Service 程式碼和 Interface 定義。所有 API 必須透過 `HttpProxyService` 進行請求處理，使用統一的 Config 物件傳遞和嚴格型別定義，確保統一的快取管理和錯誤處理機制。

## 📋 參考檔案定義

**主要參考檔案路徑：**

```
API_CONFIG_FILE = "./api-core_config.md"
SWAGGER_JSON_FILE = "./swagger.json"
```

**如果任一檔案不存在或無法存取，立刻停止後續執行並通知開發者**

### 參考檔案說明

#### API 配置檔案

-   **檔案名稱**：如上述 `API_CONFIG_FILE` 定義
-   **用途**：包含完整的 API 列表定義和檔案位置規範
-   **內容**：
    -   需要處理的 API 列表（HTTP 方法、路徑、說明、ResourceName）
    -   Service 和 Interface 檔案的輸出位置
    -   專案特定的配置資訊

#### Swagger 定義檔案

-   **檔案名稱**：如上述 `SWAGGER_JSON_FILE` 定義
-   **用途**：OpenAPI/Swagger 格式的 API 完整定義檔案
-   **內容**：
    -   所有 API 端點的完整定義（paths、parameters、responses）
    -   Schema 和資料模型定義（components）
    -   API 版本和基本資訊

**重要**：本文件中的「API 列表」、「檔案位置」和「Swagger 檔案」都將參考上述檔案定義。如需修改檔案路徑，請只更新上述檔案路徑定義即可。

## 🔧 HttpProxyService 使用規範

### 服務引用

```typescript
import { HttpProxyService } from "@shared/services/http/http-proxy.service";
```

---

## 🔄 開發流程總覽

API 自動化開發遵循以下標準化流程：

```
1. 前置檢查與準備
    ↓
2. 分析 Swagger 檔案
    ↓
3. 生成 Interface
    ↓
4. 生成 Service
    ↓
5. 驗證與整合
    ↓
6. 生成報告
```

---

## 1. 前置檢查與準備

### 1.1 必要檔案檢查

**在開始執行前，必須確保以下檔案存在：**

1. **Swagger JSON 檔案**

    - 檔案路徑：請參考上方 `SWAGGER_JSON_FILE` 定義
    - 檔案內容必須是有效的 OpenAPI/Swagger 格式
    - 包含需要處理的 API 定義

2. **API 列表定義檔案**

    - 檔案路徑：請參考上方 `API_CONFIG_FILE` 定義
    - 必須包含明確的 API 列表和對應的 ResourceName
    - 必須包含 Service 和 Interface 的檔案位置規範

3. **專案結構檢查**
    - 確認目標專案目錄存在
    - 確認 Service 和 Interface 的輸出目錄可寫入

### 1.2 執行中止條件

**如果任一檔案不存在或無法存取，立刻停止後續執行並通知開發者：**

```
❌ 執行中止：缺少必要檔案
請確認以下檔案存在後再重新執行：
1. Swagger JSON 檔案（請參考文件上方 SWAGGER_JSON_FILE 定義）
2. API 配置檔案（請參考文件上方 API_CONFIG_FILE 定義）
3. 目標專案目錄結構完整

請開發者檢查並提供所有必要檔案後再開始執行。
```

### 1.3 重複檢查規則

在生成新的 API 實作前，檢查以下項目：

#### 1.3.1 檔案存在性檢查

-   檢查目標 Service 檔案是否已存在
-   檢查目標 Interface 檔案是否已存在

#### 1.3.2 API 方法重複檢查

-   **相同 API 路徑**：檢查是否已有相同 HTTP 方法和路徑的 API 實作
-   **相同方法名稱**：檢查是否已有相同名稱的方法定義
-   **符合規範檢查**：確認現有實作是否符合本開發計劃的規範

#### 1.3.3 處理邏輯

-   **如果 API 已存在且符合規範**：跳過生成，記錄到「已存在清單」
-   **如果 API 已存在但不符合規範**：更新現有實作以符合規範
-   **如果 API 不存在**：建立新的 API 實作

#### 1.3.4 Interface 重複檢查

-   **相同型別定義**：檢查是否已有相同名稱的 Interface 定義
-   **Schema 一致性**：確認現有 Interface 是否與 swagger.json 一致
-   **如果 Interface 已存在且正確**：跳過生成，記錄到「已存在清單」

---

## 2. 分析 Swagger 檔案

### 2.1 資料來源與處理方式

-   **提供檔案**：會提供 `SWAGGER_JSON_FILE` 定義路徑的完整 swagger.json 檔案，包含所有系統的 API 定義
-   **選擇性處理**：只處理 **API 列表** 中明確指定的 API 端點
-   **忽略其他 API**：swagger.json 中其他未列出的 API 將被忽略，不進行處理

### 2.2 分析步驟

#### Step 1: 載入 Swagger 檔案

-   讀取 `SWAGGER_JSON_FILE` 定義路徑的 swagger.json 檔案
-   驗證檔案格式是否符合 OpenAPI/Swagger 規範
-   確認檔案包含 paths 和 components 區段

#### Step 2: 比對 API 列表

-   讀取 API_CONFIG_FILE 中指定的 API 列表
-   從 swagger.json 的 paths 區段中找出對應的 API 端點
-   只提取列表中明確指定的 API 定義

#### Step 3: 萃取 Schema 依賴

-   分析指定 API 的 requests 和 responses 中使用的 Schema 引用
-   遞迴收集所有相關的巢狀 Schema 和依賴關係
-   找出 API 使用的所有資料結構定義

#### Step 4: 建立處理清單

-   建立需要生成 Interface 的 Schema 清單
-   建立需要生成 Service 方法的 API 清單
-   排除 swagger.json 中其他未使用的 API 和 Schema

### 2.3 API 列表來源

**需要處理的 API 列表請參考：**

📄 **請參考文件上方 `API_CONFIG_FILE` 定義的檔案**

此檔案包含：

-   完整的 API 列表（HTTP 方法、路徑、說明、ResourceName）
-   每個 API 的詳細規格說明
-   API 對應的 Service 分類

**重要說明**：

-   只處理該檔案中列出的 API，`SWAGGER_JSON_FILE` 定義的 swagger.json 中的其他 API 將被忽略
-   需要從 `SWAGGER_JSON_FILE` 定義的 swagger.json 中找出這些 API 的完整定義（參數、回應格式等）
-   根據這些 API 使用的 Schema 生成對應的 TypeScript Interface

### 2.4 Schema 依賴分析

-   分析指定 API 使用的所有 Schema 引用
-   遞迴收集相關的巢狀 Schema 和依賴
-   建立最小化的 Schema 集合（只包含需要的部分）

---

## 3. 生成 Interface

### 3.1 檔案位置規範

**檔案位置和輸出路徑請參考：**

📄 **請參考文件上方 `API_CONFIG_FILE` 定義的檔案**

此檔案包含：

-   Interface 檔案的輸出位置規範
-   專案特定的目錄結構要求

### 3.2 Interface 生成規範

基於 `SWAGGER_JSON_FILE` 定義的 swagger.json 中指定 API 的相關 Schema，自動生成：

⚠️ 嚴禁自創：Interface 與 Enum 不得產生 Swagger 未定義的任何內容（欄位、型別、列舉成員、預設值、描述等）。型別必須「逐字鏡像」 Swagger Schema；如需擴充或調整，請先更新 Swagger 再同步產生程式碼。

#### 3.2.1 API 回應介面

-   根據 API 的 responses 區段生成對應的 TypeScript 介面
-   只生成列表中 API 使用的回應格式
-   介面命名規則：依據 `SWAGGER_JSON_FILE` 定義的 swagger.json 中的 schema 名稱或自動生成

#### 3.2.2 請求參數介面

-   提取路徑參數、查詢參數的型別定義
-   POST/PUT 請求的 body 參數介面
-   只處理指定 API 相關的參數結構

#### 3.2.3 相關業務實體

-   提取指定 API 使用到的所有相關 Schema
-   包含巢狀物件、陣列型別的完整定義
-   避免生成未使用的 Schema

#### 3.2.4 相關列舉定義

-   只生成指定 API 中使用的列舉型別
-   自動轉換為 TypeScript enum 格式
-   **重要**：列舉定義必須生成到配置檔案指定的 Enum 檔案位置，**不可**與 Interface 生成在同一個檔案
-   檔案位置：請參考 `API_CONFIG_FILE` 定義的 "Enum 檔案" 路徑

#### 3.2.5 共用型別重用規則

**重要：不要重複生成已存在的共用型別**

-   **檢查 `@shared/models/http/common.model.ts`**：此檔案包含專案共用的基礎型別定義
-   **已存在的共用型別**：
    -   `PaginatedResult<T>` - 分頁列表資料結構
    -   `StateCodeDataResult` - 錯誤回應的狀態碼資料結構
    -   `ApiResult<T>` - 標準 API 回應型別
    -   `ApiListResult<T>` - 分頁列表 API 回應型別
    -   `ErrorResponse` - 錯誤回應型別
    -   `ErrorResult` - 錯誤結果型別
-   **處理原則**：
    -   如果 Swagger Schema 對應到上述共用型別，直接使用 `@shared/models/http/common.model.ts` 中的定義
    -   不要在業務模型檔案中重複定義 `ApiResponse`、`ApiResult`、`ApiListResult` 等型別
    -   只生成業務特定的資料模型（如 `IndicatorListResponse`、`VariableResponse` 等）
    -   在生成的 Interface 檔案中，透過 import 引用共用型別

#### 3.2.6 嚴格一致性原則（與 Swagger 完全一致）

-   不得新增或移除任何屬性；必須與 Swagger 的 required/optional 設定一致
-   不得更改屬性名稱、型別、nullable、enum 值或格式描述
-   巢狀物件、陣列、泛型等結構必須與 Swagger 定義相符
-   列舉成員與其值必須完全對應 Swagger，禁止新增例如 Unknown、All 等自定義成員

### 3.3 生成步驟

#### Step 1: 分析 Schema 依賴

-   從指定 API 的 responses 和 requestBody 中找出所有引用的 Schema
-   遞迴分析巢狀 Schema 和相關依賴
-   建立最小化的 Schema 集合（只包含需要的部分）

#### Step 2: 檢查現有 Interface

-   掃描目標 Interface 檔案，檢查是否已有相同名稱的型別定義
-   **優先檢查共用型別檔案** (`@shared/models/http/common.model.ts`)，避免重複生成
-   驗證現有 Interface 是否與 `SWAGGER_JSON_FILE` 定義的 swagger.json 中的 Schema 一致
-   如果 Interface 已存在且正確，記錄到「已存在清單」
-   如果型別存在於共用檔案中，在業務模型中透過 import 引用，不要重複定義

#### Step 3: 生成 TypeScript 介面

-   根據 Schema 定義生成對應的 TypeScript 介面
-   **排除共用型別**：不生成 `ApiResult`、`ApiListResult`、`BaseApiResponse`、`PaginatedResult` 等已存在的共用型別
-   只生成業務特定的資料模型介面
-   **處理列舉型別**：不在 Interface 檔案中定義 enum，應生成到配置檔案指定的 Enum 檔案位置，然後在 Interface 檔案中 import 使用
-   確保介面命名符合專案規範
-   在檔案開頭添加必要的 import 語句以引用共用型別和列舉定義

#### Step 4: 驗證型別完整性

-   檢查生成的 Interface 是否包含所有必要屬性
-   驗證型別安全性和巢狀結構正確性
-   確保與 API 定義完全一致

### 3.4 列舉 (Enum) 生成規範

#### 3.4.1 檔案位置規範

**列舉檔案位置和輸出路徑請參考：**

📄 **請參考文件上方 `API_CONFIG_FILE` 定義的檔案**

此檔案包含：

-   Enum 檔案的輸出位置規範
-   列舉應獨立放置，不與 Interface 模型檔案混合

#### 3.4.2 列舉生成規範

##### 3.4.2.1 獨立檔案規則

-   **重要**：列舉定義 **必須** 生成到獨立的 `.ts` 檔案中，**不可** 與 Interface 定義放在同一個檔案
-   每個 ResourceName 對應一個 Enum 檔案
-   檔案命名格式：`{resource-name}.ts`
-   檔案位置規範必須遵照 `API_CONFIG_FILE` 中的 "Enum 檔案" 設定

##### 3.4.2.2 列舉內容

-   只生成指定 API 中使用的列舉型別
-   列舉應該按照在 Swagger Schema 中的定義順序組織
-   為每個列舉成員添加完整的 JSDoc 註解說明
-   資料來源僅限 Swagger 中的 enum 定義；不得新增任何列舉成員或變更其名稱、值

##### 3.4.2.3 Enum 檔案範例

```typescript
/**
 * 指標相關的列舉定義
 *
 * @module IndicatorEnums
 * @description 包含 Indicator API 使用的所有列舉定義
 */

/**
 * 指標頻率列舉
 *
 * @enum {number}
 * @description 取得數據的頻率 (秒為單位)
 */
export enum GetFrequency {
    /**
     * 1 秒
     */
    ONE_SECOND = 1,
    /**
     * 30 秒
     */
    THIRTY_SECONDS = 30,
    /**
     * 60 秒
     */
    SIXTY_SECONDS = 60,
}

// ... 其他列舉定義
```

##### 3.4.2.4 Interface 檔案中的使用

-   Interface 檔案應該在開頭透過 `import` 語句引用 Enum 檔案
-   不要在 Interface 檔案中重複定義任何列舉
-   範例：

```typescript
// indicator.model.ts
import { GetFrequency } from "../enums/indicator";

export interface IndicatorFilterRequest {
    frequency?: GetFrequency;
    // ...
}
```

### Config Interface 對照表

Service 層接收來自 Middleware 層的 Config（不含 url），並在方法內補上 `path.url`。

#### Middleware 層傳入的 Config（不含 url）

| HTTP 方法 | 用途        | Interface                                             | 備註               |
| --------- | ----------- | ----------------------------------------------------- | ------------------ |
| GET       | 一般請求    | `MiddlewareGetRequestConfig<TFilter>`                 | 不需要 body        |
| GET       | Offset 分頁 | `MiddlewareGetListByOffsetRequestConfig<TFilter>`  | offset, limit 必填 |
| GET       | ID 分頁     | `MiddlewareGetListByIdRequestConfig<TFilter>`      | id, limit 必填     |
| POST      | 新增資源    | `MiddlewareMutationRequestConfig<TBody, TFilter>`     | body 在 config 中  |
| PUT       | 更新資源    | `MiddlewareMutationRequestConfig<TBody, TFilter>`     | body 在 config 中  |
| DELETE    | 刪除資源    | `MiddlewareDeleteRequestConfig<TFilter>`              | 不需要 body        |

### 參數傳遞規則

-   **統一使用 Config 物件**：所有參數透過 Config 物件傳遞
-   **Middleware 層傳入**：`serviceUuid`、`queryParams`、`path.variables`、`path.keys`、`body`（POST/PUT）
-   **Service 層補上**：`path.url`（API 路徑）
-   **keys 必須是字串字面量**：例如 `{ offset: '{offset}', limit: '{limit}' }`

---

## 4. 生成 Service

### 4.1 檔案位置規範

**檔案位置和輸出路徑請參考：**

📄 **請參考文件上方 `API_CONFIG_FILE` 定義的檔案**

此檔案包含：

-   Service 檔案的輸出位置規範
-   專案特定的目錄結構要求

### 4.2 Service 命名規則

#### 4.2.1 命名規則邏輯

1. **PascalCase 格式**：Service 名稱使用 PascalCase 命名法（每個單字首字母大寫）
2. **添加 Service 後綴**：最終格式為 `{ResourceName}Service`

#### 4.2.2 合併規則

**同一 ResourceName 的所有 API 必須寫在同一個 Service 檔案中**，檔案位置依照 API 列表中的 ResourceName 欄位內容決定，不可自行判斷：

-   檔案命名格式：`{ResourceName}Service`
-   檔案位置：請參考上方 `API_CONFIG_FILE` 定義檔案中的輸出位置規範
-   API 分組：依據 API 列表中的 ResourceName 欄位進行分組

### 4.3 HTTP 方法對應

| HTTP 方法 | HttpProxyService 方法                                                                       | 用途     |
| --------- | ------------------------------------------------------------------------------------------- | -------- |
| GET       | `getResource` / `getListByOffset` / `getAllListByOffset` / `getListById` / `getAllListById` | 取得資源 |
| POST      | `postResource`                                                                              | 建立資源 |
| PUT       | `putResource`                                                                               | 更新資源 |
| DELETE    | `deleteResource`                                                                            | 刪除資源 |

#### 4.3.1 回傳型別強制規範（無例外）

-   所有 Service 方法回傳型別一律為 Observable<ApiResult<T>> 或 Observable<ApiListResult<T>>
-   映射規則：
    -   getResource → Observable<ApiResult<T>>
    -   getListByOffset / getAllListByOffset / getListById / getAllListById → Observable<ApiListResult<T>>
    -   postResource / putResource / deleteResource → Observable<ApiResult<T>>
-   禁止回傳：T、T[]、BaseApiResponse、HttpResponse<any>、Promise<any>、any 等非標準封裝
-   使用之共用型別來源：`@shared/models/http/common.model.ts`

### 4.4 GET API 分類決策

GET API 根據路徑特徵分為兩種主要類型：

#### 4.4.1 單一資源 (getResource)

**特徵識別**：

-   所有非 List 形式的 GET API
-   可能包含參數，也可能不包含參數
-   回傳單一物件或特定資源

**範例**：

```
GET /v1/topology-setting/model/{id}/attributes
GET /v1/translation/enumeration/{name}
GET /v1/topology-setting/io-setting/udid/{ids}
GET /v1/system/status
```

#### 4.4.2 OFFSET 列表資源 (同時需要實作兩個方法)

**特徵識別**：

-   路徑包含 `{offset}` 和 `{limit}` 參數
-   需要同時實作分頁查詢和取得全部資料兩個方法
-   **分頁方法**：使用 `getListByOffset`
-   **全部方法**：使用 `getAllListByOffset`

**範例**：

```
GET /v1/topology-setting/device/offset/{offset}/limit/{limit}/list
GET /v1/user/offset/{offset}/limit/{limit}/list
```

### 4.4.3 ID 列表資源 (同時需要實作兩個方法)

**特徵識別**：

-   路徑包含 `{id}` 參數
-   需要同時實作依 ID 分頁查詢和取得全部資料兩個方法
-   **分頁方法**：使用 `getListById`
-   **全部方法**：使用 `getAllListById`

**範例**：

```GET /v1/topology-setting/device/{id}/limit/{limit}/list
GET /v1/user/{id}/limit/{limit}/list
```

### 4.5 API 分類決策樹

```
收到API規格
    ↓
HTTP方法判斷
    ├─ GET → 路徑分析
    │   ├─ 包含 {offset} 和 {limit} 參數
    │   │   ├─ 建立 getListByOffset 方法 (分頁查詢)
    │   │   └─ 建立 getAllListByOffset 方法 (取得全部)
    │   ├─ 包含 {id} 和 {limit} 參數
    │   │   ├─ 建立 getListById 方法 (依ID分頁查詢)
    │   │   └─ 建立 getAllListById 方法 (依ID取得全部)
    │   └─ 不包含 {offset} 和 {limit} 參數 → getResource (單一資源)
    ├─ POST → postResource
    ├─ PUT → putResource
    └─ DELETE → deleteResource
```

### 4.6 快取機制規範

#### 4.6.1 useCache 參數說明

所有 API 方法都支援 `useCache` 參數：

-   **型別**：`boolean`
-   **預設值**：`false`
-   **用途**：控制是否啟用快取機制
-   **傳遞方式**：透過 Config 物件中的 `useCache` 屬性

### 4.7 基本 Service 結構模板

```typescript
import { Injectable } from '@angular/core';
import { HttpProxyService } from '@shared/services/http/http-proxy.service';
import { ApiListResult, ApiResult } from '@shared/models/http/common.model';
import { VersionEnum } from 'core/enums/version';

@Injectable()
export class {ServiceName} {
    private readonly httpProxyService = inject(HttpProxyService); 
    private readonly API_URL = `${ApiUrl}/${VersionEnum.v1}`;

    // API 路徑定義 - 完整路徑列表
    // {在此區域定義所有API路徑常數}

    constructor() {}

    // {在此區域實作所有API方法}
}
```

### 4.8 API 路徑定義規範

**命名規則**：`{httpMethod}{PathBasedName}Path`

**Path 屬性名稱須從 path 中判斷**，範例如下：

#### 4.8.1 從 Path 提取方法名稱規則

**單一資源 API 範例**：

```typescript
// GET /v1/topology-setting/io-setting/udid/{id}/io-point-identifier/{ioPointIdentifier}/function-test
private readonly getFunctionTestPath = `${this.API_URL}/topology-setting/io-setting/udid/{id}/io-point-identifier/{ioPointIdentifier}/function-test`;
// 方法名：getFunctionTest()
// Interface：FunctionTestModel

// GET /v1/topology-setting/model/{id}/attributes
private readonly getAttributesPath = `${this.API_URL}/topology-setting/model/{id}/attributes`;
// 方法名：getAttributes()
// Interface：AttributesModel
```

**列表資源 API 範例**：

```typescript
// 依偏移量
// GET /v1/topology-setting/io-setting/udid/offset/{offset}/limit/{limit}/list
private readonly getUdidListPath = `${this.API_URL}/topology-setting/io-setting/udid/offset/{offset}/limit/{limit}/list`;
// 分頁方法名：getUdidListByOffset()
// 全部方法名：getAllUdidListByOffset()
// Interface：UdidListModel

// 依ID
// GET /v1/topology-setting/io-setting/udid/{ids}/limit/{limit}/list
private readonly getUdidByIdsPath = `${this.API_URL}/topology-setting/io-setting/udid/{ids}/limit/{limit}/list`;
// 分頁方法名：getUdidByIds()
// 全部方法名：getAllUdidByIds()
// Interface：UdidListModel
```

**修改資源 API 範例**：

```typescript
// PUT /v1/topology-setting/io-setting/udid/replace
private readonly putUdidReplacePath = `${this.API_URL}/topology-setting/io-setting/udid/replace`;
// 方法名：putUdidReplace()
// Interface：UdidReplaceRequest, UdidReplaceResponse
```

#### 4.8.2 命名衝突處理規則

**如有重複名稱，從 Path 上找尋其他關鍵字做區別**：

-   加入路徑中的關鍵字段（如：`IoSetting`, `Model`等）
-   或加入 `Request` 或 `Response` 做差異化
-   範例：`getFunctionTestRequest`, `getFunctionTestResponse`

### 4.9 方法實作範例

**重要**：Service 層接收 Middleware 層傳入的 Config（不含 url），並在方法內使用展開運算子補上 `path.url`。

#### 4.9.1 列表資源 API (需同時實作兩個方法)

**分頁列表方法**：

```typescript
// 路徑: /v1/topology-setting/io-setting/udid/offset/{offset}/limit/{limit}/list
private readonly getUdidListPath = `${this.API_URL}/topology-setting/io-setting/udid/offset/{offset}/limit/{limit}/list`;

/**
 * 取得 UDID 清單（分頁）
 * @param config 請求配置物件（來自 Middleware 層，包含 offset, limit）
 * @returns Observable<ApiListResult<UdidListResponse>>
 */
getUdidListByOffset(
    config: MiddlewareGetListByOffsetRequestConfig<Record<string, any>>
): Observable<ApiListResult<UdidListResponse>> {
    return this.httpProxyService.getListByOffset<UdidListResponse>({
        ...config,
        path: {
            ...config.path,
            url: this.getUdidListPath
        }
    });
}

/**
 * 取得全部 UDID 清單
 * @param config 請求配置物件（來自 Middleware 層，包含 offset, limit）
 * @returns Observable<ApiListResult<UdidListResponse>>
 */
getAllUdidListByOffset(
    config: MiddlewareGetListByOffsetRequestConfig<Record<string, any>>
): Observable<ApiListResult<UdidListResponse>> {
    return this.httpProxyService.getAllListByOffset<UdidListResponse>({
        ...config,
        path: {
            ...config.path,
            url: this.getUdidListPath
        }
    });
}
```

**Middleware 層呼叫範例**：

```typescript
// Middleware 層傳入 config，包含 offset, limit 在 path.variables 中
this.coreService.getUdidListByOffset({
    serviceUuid: this._serviceUuid,
    queryParams: { filter },
    path: {
        variables: { offset: 0, limit: this.limit },
        keys: { offset: '{offset}', limit: '{limit}' }
    }
});
```

#### 4.9.2 單一資源 API

**單一資源方法（有參數）**：

```typescript
// 路徑: /v1/topology-setting/io-setting/udid/{id}/function-test
private readonly getFunctionTestPath = `${this.API_URL}/topology-setting/io-setting/udid/{id}/function-test`;

/**
 * 取得指定UDID的功能測試
 * @param config 請求配置物件（來自 Middleware 層，不含 url）
 * @returns Observable<ApiResult<FunctionTestModel>>
 */
getFunctionTest(
    config: MiddlewareGetRequestConfig<Record<string, any>>
): Observable<ApiResult<FunctionTestModel>> {
    return this.httpProxyService.getResource<FunctionTestModel>({
        ...config,
        path: {
            ...config.path,
            url: this.getFunctionTestPath
        }
    });
}
```

**單一資源方法（無參數）**：

```typescript
// 路徑: /v1/topology-setting/io-setting/scan
private readonly getScanPath = `${this.API_URL}/topology-setting/io-setting/scan`;

/**
 * 取得掃描結果
 * @param config 請求配置物件（來自 Middleware 層，不含 url）
 * @returns Observable<ApiResult<ScanModel>>
 */
getScan(
    config: MiddlewareGetRequestConfig<Record<string, any>>
): Observable<ApiResult<ScanModel>> {
    return this.httpProxyService.getResource<ScanModel>({
        ...config,
        path: {
            ...config.path,
            url: this.getScanPath
        }
    });
}
```

#### 4.9.3 多筆資源 API (單一方法，特殊參數處理)

```typescript
// 路徑: /v1/topology-setting/io-setting/udid/{ids}
private readonly getUdidByIdsPath = `${this.API_URL}/topology-setting/io-setting/udid/{ids}`;

/**
 * 依指定 ID 取得 UDID 列表
 * @param config 請求配置物件（來自 Middleware 層，包含 ids）
 * @returns Observable<ApiResult<UdidListResponse>>
 */
getUdidByIds(
    config: MiddlewareGetRequestConfig<Record<string, any>>
): Observable<ApiResult<UdidListResponse>> {
    return this.httpProxyService.getResource<UdidListResponse>({
        ...config,
        path: {
            ...config.path,
            url: this.getUdidByIdsPath
        }
    });
}
```

**Middleware 層呼叫範例**：

```typescript
// Middleware 層傳入 config，包含 ids 在 path.variables 中
this.coreService.getUdidByIds({
    serviceUuid: this._serviceUuid,
    queryParams: { filter },
    path: {
        variables: { ids },
        keys: { ids: '{ids}' }
    }
});
```

#### 4.9.4 POST API

```typescript
// 路徑: /v1/topology-setting/io-setting/udid
private readonly postUdidPath = `${this.API_URL}/topology-setting/io-setting/udid`;

/**
 * 建立 UDID 設定
 * @param config 請求配置物件（來自 Middleware 層，包含 body）
 * @returns Observable<ApiResult<UdidResponse>>
 */
postUdid(
    config: MiddlewareMutationRequestConfig<UdidRequest, Record<string, any>>
): Observable<ApiResult<UdidResponse>> {
    return this.httpProxyService.postResource<UdidResponse>({
        ...config,
        path: {
            ...config.path,
            url: this.postUdidPath
        }
    });
}
```

**Middleware 層呼叫範例**：

```typescript
// Middleware 層傳入 config，body 放在 config 中
this.coreService.postUdid({
    serviceUuid: this._serviceUuid,
    queryParams: { filter },
    body: data
});
```

#### 4.9.5 PUT API

```typescript
// 路徑: /v1/topology-setting/io-setting/udid/{id}
private readonly putUdidPath = `${this.API_URL}/topology-setting/io-setting/udid/{id}`;

/**
 * 更新 UDID 設定
 * @param config 請求配置物件（來自 Middleware 層，包含 body 和 id）
 * @returns Observable<ApiResult<UdidResponse>>
 */
putUdid(
    config: MiddlewareMutationRequestConfig<UdidRequest, Record<string, any>>
): Observable<ApiResult<UdidResponse>> {
    return this.httpProxyService.putResource<UdidResponse>({
        ...config,
        path: {
            ...config.path,
            url: this.putUdidPath
        }
    });
}
```

**Middleware 層呼叫範例**：

```typescript
// Middleware 層傳入 config，包含 id 和 body
this.coreService.putUdid({
    serviceUuid: this._serviceUuid,
    queryParams: { filter },
    path: {
        variables: { id },
        keys: { id: '{id}' }
    },
    body: data
});
```

#### 4.9.6 DELETE API

```typescript
// 路徑: /v1/topology-setting/io-setting/udid/{ids}
private readonly deleteUdidPath = `${this.API_URL}/topology-setting/io-setting/udid/{ids}`;

/**
 * 刪除 UDID 設定
 * @param config 請求配置物件（來自 Middleware 層，包含 ids）
 * @returns Observable<ApiResult<boolean>>
 */
deleteUdid(
    config: MiddlewareDeleteRequestConfig<Record<string, any>>
): Observable<ApiResult<boolean>> {
    return this.httpProxyService.deleteResource<boolean>({
        ...config,
        path: {
            ...config.path,
            url: this.deleteUdidPath
        }
    });
}
```

**Middleware 層呼叫範例**：

```typescript
// Middleware 層傳入 config，包含 ids
this.coreService.deleteUdid({
    serviceUuid: this._serviceUuid,
    queryParams: { filter },
    path: {
        variables: { ids },
        keys: { ids: '{ids}' }
    }
});
```

### 4.10 生成步驟

#### Step 1: 檢查現有實作

-   掃描目標 Service 檔案，檢查是否已有相同的 API 實作
-   比對方法名稱、HTTP 方法和路徑是否相符
-   檢查現有實作是否符合本開發計劃的規範

#### Step 2: 分析 API 定義

-   從 `SWAGGER_JSON_FILE` 定義的 swagger.json 中提取指定 API 的完整定義
-   確認 HTTP 方法、路徑參數、請求/回應格式
-   根據決策樹選擇對應的 HttpProxyService 方法

#### Step 3: 確定型別定義

-   使用從 Swagger Schema 生成的具體型別介面
-   為請求參數定義具體型別（如：CreateDeviceRequest）
-   為回應資料定義具體型別（如：DeviceListResponse）

#### Step 4: 生成方法實作

-   根據 API 定義建立對應的 Service 方法
-   遵循決策樹選擇正確的 HttpProxyService 方法
-   確保方法簽名包含所有必要參數（包括 serviceUuid 和 useCache）

#### Step 5: 檔案整合

-   將同一 ResourceName 的 API 整合到同一 Service 檔案
-   依照 API_CONFIG_FILE 中的檔案位置規範放置檔案
-   確保 import 語句和依賴關係正確

---

## 5. 驗證與整合

### 5.1 程式碼整合檢查

#### Step 1: 型別一致性驗證

-   確保 Interface 和 Service 中使用的型別名稱一致
-   檢查所有 API 方法的參數和回傳值型別正確
-   驗證沒有使用 any 型別
-   驗證所有 Service 方法回傳皆為 ApiResult<T> 或 ApiListResult<T>，不得出現其他封裝或裸型別

#### Step 2: 檔案結構驗證

-   檢查同一 ResourceName 的 API 是否正確整合到同一 Service 檔案
-   驗證檔案位置是否符合 API_CONFIG_FILE 中的規範
-   確認所有必要的 import 語句都已正確添加

#### Step 3: 方法簽名檢查

-   驗證每個 API 方法都包含必要的參數（serviceUuid、useCache 等）
-   檢查 HttpProxyService 方法的選擇是否正確
-   確認 URL 參數的處理方式正確
-   確認 useCache 參數正確設定為可選參數且預設值為 false

### 5.2 程式碼品質驗證

#### Step 1: 註解完整性檢查

-   驗證每個方法都有完整的 JSDoc 註解
-   檢查參數說明和回傳值描述是否準確
-   確認註解格式符合專案規範
-   確保 useCache 參數有適當的說明

#### Step 2: 命名規範檢查

-   驗證方法命名是否符合規範
-   檢查 API 路徑常數的命名是否正確
-   確認 Service 檔案命名格式正確

#### Step 3: 錯誤處理檢查

-   確認所有 API 方法都正確使用 HttpProxyService
-   檢查 useCache 參數的預設值設定
-   驗證服務唯一識別碼參數的使用

### 5.3 處理範例檢查

假設檢查現有檔案發現以下情況：

**現有 {ResourceName}Service 檔案中已包含：**

-   `{existingApiMethod1}()` 方法 ✅ 符合規範
-   `{existingApiMethod2}()` 方法 ✅ 符合規範
-   `{oldApiMethod}()` 方法 ❌ 不在 API 列表中

**需要新增的方法：**

-   `{newApiMethod1}()` - {功能描述 1}
-   `{newApiMethod2}()` - {功能描述 2}
-   `{newApiMethod3}()` - {功能描述 3}

**處理結果**：

-   保留符合規範的現有 API
-   新增缺少的 API 方法
-   忽略不在列表中的舊方法

---

## 6. 生成報告

### 6.1 執行完成報告格式

執行完成後，提供以下格式的詳細報告：

```
✅ API 自動化開發完成報告

📁 處理的檔案：
- Service 檔案：{生成或更新的Service檔案路徑}
- Interface 檔案：{生成或更新的Interface檔案路徑}

✅ 已存在且可直接引用的API：
┌─────────────────────────────────────────────────────────────┐
│ ResourceName: {ResourceName1}Service                        │
├─────────────────────────────────────────────────────────────┤
│ • {existingApiMethod1}() - {功能描述1}                       │
│ • {existingApiMethod2}() - {功能描述2}                       │
│ • {existingApiMethod3}() - {功能描述3}                       │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ ResourceName: {ResourceName2}Service                        │
├─────────────────────────────────────────────────────────────┤
│ • {existingApiMethod4}() - {功能描述4}                       │
│ • {existingApiMethod5}() - {功能描述5}                       │
└─────────────────────────────────────────────────────────────┘

🆕 新增的API：
┌─────────────────────────────────────────────────────────────┐
│ ResourceName: {ResourceName1}Service                        │
├─────────────────────────────────────────────────────────────┤
│ • {newApiMethod1}() - {功能描述1}                            │
│ • {newApiMethod2}() - {功能描述2}                            │
│ • {newApiMethod3}() - {功能描述3}                            │
└─────────────────────────────────────────────────────────────┘

💡 使用建議：
- 已存在的API可直接在Component中引用使用
- 新增的API已遵循專案規範，可立即投入使用
- 所有API都支援快取機制，可透過 Config 物件中的 useCache 屬性控制
- 建議在適當時機啟用快取以提升效能
```

### 6.2 報告內容說明

**最終報告會明確列出：**

1. **已存在可直接使用的 API**（避免重複開發）
2. **新增的 API**（開發者可立即使用）
3. **被忽略的舊方法**（不在 API 列表中）
4. **處理的檔案路徑**（方便開發者檢查）
5. **快取機制說明**（提醒開發者善用快取功能）

---

## 📋 注意事項

1. **必須使用 HttpProxyService**：所有 API 請求都必須透過 `@shared/services/http/http-proxy.service` 進行
2. **Config 物件傳遞**：所有參數透過 Config 物件傳遞，使用對應的 Interface 類型
3. **路徑參數**：使用 `path.variables` 和 `path.keys`（keys 必須是字串字面量）
4. **serviceUuid 必傳**：所有方法必須在 Config 中傳入 `serviceUuid`
5. **快取控制**：透過 Config 物件中的 `useCache` 屬性控制，預設值為 false
6. **錯誤處理**：HttpProxyService 已內建錯誤處理，Service 層不需額外處理
7. **型別安全**：必須使用從 Swagger Schema 生成的具體型別介面，不可使用 any 型別
8. **註解完整性**：每個方法都必須包含完整的 JSDoc 註解
9. **共用型別重用**：不要重複生成 `@shared/models/http/common.model.ts` 中已存在的共用型別（如 `ApiResult`、`ApiListResult` 等），應直接 import 使用
10. **列舉檔案分離**：

-   ⚠️ **重要**：列舉定義 **必須生成到獨立的 Enum 檔案**，不可與 Interface 模型檔案混合
-   在配置檔案指定的 Enum 檔案位置生成列舉
-   Interface 檔案應透過 `import` 引用列舉，不要重複定義
-   列舉檔案應包含 API 使用的所有列舉型別定義

11. **版本導入規範**：

-   ⚠️ **重要**：所有 Service 檔案都必須使用 `import { VersionEnum } from 'core/enums/version'`
-   在 `API_URL` 定義中使用 `VersionEnum.v1` 而非硬編碼版本號
-   確保版本控制集中管理

12. **Swagger 嚴格對應**：Interface 與 Enum 內容必須嚴格來源於 Swagger，禁止自創欄位或列舉成員
13. **回傳型別強制規範**：所有 Service 方法回傳必須使用 `ApiResult` 或 `ApiListResult`（共用封裝），不允許任何例外
14. **依賴注入規範**：禁止使用 constructor 注入，必須使用 Angular 的 `inject()` 函式進行注入

---

## 📝 總結

這個開發計劃提供了完整的 API 自動化接入流程，確保所有 API 都能夠以統一、規範的方式進行實作。透過標準化的開發流程與 Config 物件傳遞方式，開發者可以快速、準確地生成高品質的 Service 和 Interface 程式碼，提升開發效率並確保程式碼品質。使用重構後的 HttpProxyService 與嚴格型別定義，讓開發者能夠根據具體需求靈活使用快取功能，進一步提升應用程式效能。
