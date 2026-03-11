
# 你的任務
根據使用者提供的核心服務檔案路徑（單一或多個），為每個核心服務在指定的目標資料夾中生成或更新封裝服務檔案。

## 📋 參考檔案定義

**主要參考檔案路徑：**
```
Middleware_CONFIG_FILE = "./api-middleware_config.md"
```
**如果配置檔案不存在或無法存取，立刻停止後續執行並通知開發者**

### 參考檔案說明

#### 服務配置檔案
- **檔案名稱**：如上述 `Middleware_CONFIG_FILE` 定義
- **用途**：包含核心服務到目標資料夾的檔案映射關係
- **內容**：
  - 核心服務檔案路徑（從 `./src/app` 開始）
  - 每個核心服務對應的目標資料夾路徑
  - 產出檔案路徑規則：`{目標資料夾}/api/{核心服務檔名}.middleware.ts`

## 設定變數

**檔案映射關係請參考：**

📄 **請參考上方 `Middleware_CONFIG_FILE` 定義的配置檔案**

## 使用規則

1. **指定檔案處理**：根據 `Middleware_CONFIG_FILE` 中的映射表處理指定的核心服務檔案
2. **路徑規則**：
   - 核心服務檔案路徑必須從 `./src/app` 開始
   - 目標資料夾由映射表指定
   - 產出檔案路徑：`{目標資料夾}/api/{核心服務檔名}.middleware.ts`
3. **檔案處理規則**：
   - 如果目標檔案不存在：建立新檔案並生成所有方法
   - 如果目標檔案已存在：🚫 **只補充缺少的方法，絕對禁止異動既有程式碼**
   - 新方法必須插入在 `destroy()` 方法之前
4. **方法智能處理**：
   - 讀取核心服務的所有 public 方法
   - 為每個方法生成對應的封裝方法
   - 自動判斷方法類型 (查詢/異動) 並套用對應規範
5. **完整性報告**：
   - 列出處理的所有核心服務檔案
   - 報告每個檔案的處理狀態 (新建/更新/跳過)
   - 列出新增的方法清單
6. **遵循開發步驟**：按照文件中的步驟依序執行

**🚫 絕對禁止事項**：

- 🚫 **嚴禁覆蓋目標檔案中已存在的方法實作**
- 🚫 **嚴禁刪除目標檔案中的既有程式碼**
- 🚫 **嚴禁修改使用者自訂的業務邏輯**
- 🚫 **嚴禁異動既有檔案中的任何程式碼**
- 必須一步一步依照開發流程走
- 嚴禁跳過檔案存在性檢查
- 嚴禁擅自修改配置檔案中的路徑映射規則

## 完整範本

```typescript
import { Injectable } from '@angular/core';
import { firstValueFrom, catchError, map, of } from 'rxjs';
import { ApiListResult, ApiResult, ErrorResult } from '@shared/models/http/common.model';
import { TemplateModule, TemplateRequest } from '../core/api/dto/template.dto';
import { TemplateService as CoreTemplateService } from '../../core/api/services/template.service';
import { CommonService } from '@shared/services/http/common.service';
import { HttpProxyService } from '@shared/services/http/http-proxy.service';

@Injectable()
export class TemplateMiddlewareService {
    private readonly coreTemplateService = inject(CoreTemplateService);
    private readonly httpProxyService = inject(HttpProxyService); 
    private readonly commonService = inject(CommonService);
    
    readonly limit: number = 50;
    private readonly _serviceUuid: string;

    constructor() {
        this._serviceUuid = `template-service-${this.commonService.renderServiceUuid()}`;
    }

    /**
     * 取得模板列表 - 查詢方法範例 (Offset 分頁)
     * @param offset 起始位置
     * @param limit 取得數量
     * @param filter 篩選條件
     * @returns Promise<TemplateModule[]>
     */
    async getTemplateList(offset: number, limit: number, filter?: Record<string, any>): Promise<TemplateModule[]> {
        return firstValueFrom(
            this.coreTemplateService.getTemplateList({
                serviceUuid: this._serviceUuid,
                queryParams: { filter },
                path: {
                    variables: { offset, limit },
                    keys: { offset: '{offset}', limit: '{limit}' }
                }
            }).pipe(
                map((result: ApiListResult<TemplateModule>) => {
                    return result.success && result.data?.result ? result.data.result : [];
                }),
                catchError((error: ErrorResult) => {
                    console.error('TemplateService getTemplateList error:', error);
                    return of([]);
                })
            )
        );
    }

    /**
     * 取得單一模板 - 單一物件查詢範例
     * @param id 模板ID
     * @param filter 篩選條件
     * @returns Promise<TemplateModule | null>
     */
    async getTemplate(id: string, filter?: Record<string, any>): Promise<TemplateModule | null> {
        return firstValueFrom(
            this.coreTemplateService.getTemplate({
                serviceUuid: this._serviceUuid,
                queryParams: { filter },
                path: {
                    variables: { id },
                    keys: { id: '{id}' }
                }
            }).pipe(
                map((result: ApiResult<TemplateModule>) => {
                    return result.success && result.data ? result.data : null;
                }),
                catchError((error: ErrorResult) => {
                    console.error('TemplateService getTemplate error:', error);
                    return of(null);
                })
            )
        );
    }

    /**
     * 建立模板 - POST方法範例
     * @param data 模板資料
     * @param filter 篩選條件
     * @returns Promise<ApiResult<TemplateModule>>
     */
    async postTemplate(data: TemplateRequest, filter?: Record<string, any>): Promise<ApiResult<TemplateModule>> {
        return firstValueFrom(
            this.coreTemplateService.postTemplate({
                serviceUuid: this._serviceUuid,
                queryParams: { filter },
                body: data
            }).pipe(
                map((result: ApiResult<TemplateModule>) => result),
                catchError((error: ErrorResult) => {
                    console.error('TemplateService postTemplate error:', error);
                    throw error;
                })
            )
        );
    }

    /**
     * 更新模板 - PUT方法範例
     * @param id 模板ID
     * @param data 更新資料
     * @param filter 篩選條件
     * @returns Promise<ApiResult<TemplateModule>>
     */
    async putTemplate(id: string, data: TemplateRequest, filter?: Record<string, any>): Promise<ApiResult<TemplateModule>> {
        return firstValueFrom(
            this.coreTemplateService.putTemplate({
                serviceUuid: this._serviceUuid,
                queryParams: { filter },
                path: {
                    variables: { id },
                    keys: { id: '{id}' }
                },
                body: data
            }).pipe(
                map((result: ApiResult<TemplateModule>) => result),
                catchError((error: ErrorResult) => {
                    console.error('TemplateService putTemplate error:', error);
                    throw error;
                })
            )
        );
    }

    /**
     * 刪除模板 - DELETE方法範例
     * @param id 模板ID
     * @param filter 篩選條件
     * @returns Promise<ApiResult<boolean>>
     */
    async deleteTemplate(id: string, filter?: Record<string, any>): Promise<ApiResult<boolean>> {
        return firstValueFrom(
            this.coreTemplateService.deleteTemplate({
                serviceUuid: this._serviceUuid,
                queryParams: { filter },
                path: {
                    variables: { id },
                    keys: { id: '{id}' }
                }
            }).pipe(
                map((result: ApiResult<boolean>) => result),
                catchError((error: ErrorResult) => {
                    console.error('TemplateService deleteTemplate error:', error);
                    throw error;
                })
            )
        );
    }

    destroy(): void {
        this.httpProxyService.clearCacheByServiceUuid(this._serviceUuid);
    }
}
```

## 錯誤處理與回傳值規範

### 1. 查詢方法 (GET)
- **陣列查詢**：回傳 `Promise<T[]>`，使用 `map` 返回陣列或空陣列 `[]`
- **單一物件查詢**：回傳 `Promise<T | null>`，使用 `map` 返回物件或 `null`
- **錯誤處理**：使用 `catchError((error: ErrorResult) => {...})`，console.error 記錄錯誤後使用 `of([])` 或 `of(null)` 返回預設值

### 2. 異動方法 (POST, PUT, DELETE)
- **回傳完整 ApiResult**：回傳 `Promise<ApiResult<T>>`
- **使用 map 直接返回結果**：`map((result: ApiResult<T>) => result)`
- **錯誤處理**：使用 `catchError((error: ErrorResult) => {...})`，console.error 記錄錯誤後 `throw error`
- **讓呼叫方處理成功/失敗邏輯**

### 3. RxJS 寫法規範
- **必須使用 firstValueFrom 搭配 .pipe**
- **import 必須包含**：`firstValueFrom, catchError, map, of` from `'rxjs'`
- **import 必須包含**：`ErrorResult` from `'@shared/models/http/common.model'`
- **catchError 型別必須明確指定**：`catchError((error: ErrorResult) => {...})`
- **查詢方法使用 of() 返回預設值**，異動方法使用 throw error

### 4. 特殊處理方法
- **功能測試類方法**：依據業務需求決定回傳格式，通常遵循查詢方法規範

## 開發步驟

### 步驟 0：前置檢查與準備

1. **配置檔案檢查**：
   - 確認 `Middleware_CONFIG_FILE` 定義的配置檔案存在
   - 讀取並解析配置檔案中的映射表
   - 驗證配置檔案格式正確

2. **執行中止條件**：
   - 如果配置檔案不存在，立刻停止並提示錯誤
   - 如果配置檔案格式錯誤，立刻停止並提示修正

### 步驟 1：解析核心服務檔案與目標路徑

1. **讀取映射關係**：
   - 從 `Middleware_CONFIG_FILE` 中讀取核心服務與目標資料夾的映射表
   - 提取所有需要處理的核心服務檔案路徑（從 `.src/app` 開始）
   - 提取對應的目標資料夾路徑

2. **驗證檔案存在性**：
   - 逐一檢查每個核心服務檔案是否存在
   - 不存在則記錄錯誤並跳過該檔案

3. **確定產出檔案路徑**：
   - 產出路徑規則：`{目標資料夾}/api/{核心服務檔名}.middleware.ts`
   - 範例：
     ```
     核心服務：./src/app/main/core/api/services/device.service.ts
     目標資料夾：./src/app/main/features/ip-address
     產出檔案：./src/app/main/features/ip-address/api/device.middleware.ts
     ```

4. **提取服務資訊**：
   - 服務名稱（例如：`DeviceService` → `DeviceMiddlewareService`）
   - 所有 public 方法及其簽名
   - import 的模型檔案路徑

### 步驟 2：檢查目標檔案狀態並處理

針對每個核心服務，檢查目標資料夾下的產出檔案：

**產出路徑範例**：
```typescript
// 範例 1：標準產出
核心服務：./src/app/main/core/api/services/device.service.ts
目標資料夾：./src/app/main/features/ip-address
產出檔案：./src/app/main/features/ip-address/api/device.middleware.ts

// 範例 2：複合名稱
核心服務：./src/app/main/core/api/services/io-setting.service.ts
目標資料夾：./src/app/main/features/io-configuration
產出檔案：./src/app/main/features/io-configuration/api/io-setting.middleware.ts
```

**檔案狀態處理**：

- **情況 A - 檔案不存在**：
  - 自動建立目標資料夾（如果不存在）
  - 建立新的封裝服務檔案
  - 使用完整範本生成所有方法
  - 進入步驟 3

- **情況 B - 檔案已存在**：
  - 🚫 **關鍵規則：絕對禁止異動既有程式碼**
  - 讀取現有檔案內容
  - 解析已存在的方法列表
  - 比對核心服務方法，找出缺少的方法
  - **只補充缺少的方法**，插入在 `destroy()` 方法之前
  - **不修改、不刪除任何既有方法**
  - 進入步驟 3

### 步驟 3：方法生成與補充

根據核心服務的方法類型，生成對應的封裝方法：

1. **自動判斷方法類型**：
   - 方法名稱以 `get` 或 `getAll` 開頭 → 查詢方法
   - 方法名稱以 `post`, `put`, `delete` 開頭 → 異動方法
   - 其他方法 → 依據回傳值判斷

2. **套用對應規範**：
   - 查詢方法：回傳 `Promise<T[]>` 或 `Promise<T | null>`
   - 異動方法：回傳 `Promise<ApiResult<T>>`
   - 嚴格遵循錯誤處理與回傳值規範

3. **檔案處理**：
   - 新檔案：直接寫入完整內容
   - 既有檔案：在適當位置插入新方法（`destroy()` 方法之前）

### 步驟 4：生成處理報告

完成所有檔案處理後，生成詳細報告：

```markdown
## 封裝服務生成報告

### 配置來源
- 配置檔案：`{Middleware_CONFIG_FILE}`
- 處理的核心服務檔案數量：{count}

### 處理明細

#### ✅ {ServiceName}MiddlewareService
- **核心服務路徑**：`./src/app/main/core/api/services/device.service.ts`
- **目標資料夾**：`./src/app/main/features/ip-address`
- **產出檔案**：`./src/app/main/features/ip-address/api/device.middleware.ts`
- **處理狀態**：✨ 新建 / 🔄 更新
- **核心服務方法數**：{count}
- **新增方法**：
  - `getDeviceList()` - 查詢方法
  - `postDevice()` - 異動方法
- **跳過方法**：
  - `getDevice()` - 已存在

#### ✅ {ServiceName}MiddlewareService
- **核心服務路徑**：`./src/app/main/core/api/services/io-setting.service.ts`
- **目標資料夾**：`./src/app/main/features/io-configuration`
- **產出檔案**：`./src/app/main/features/io-configuration/api/io-setting.middleware.ts`
- **處理狀態**：🔄 更新
- **核心服務方法數**：{count}
- **新增方法**：
  - `getIoSettings()` - 查詢方法
  - `updateIoSetting()` - 異動方法

#### ⚠️ {ServiceName}MiddlewareService
- **核心服務路徑**：`./src/app/main/core/api/services/system-update.service.ts`
- **目標資料夾**：`./src/app/main/features/system-management`
- **產出檔案**：`./src/app/main/features/system-management/api/system-update.middleware.ts`
- **處理狀態**：⏭️ 跳過
- **原因**：目標檔案已是最新狀態，無需更新

#### ❌ {ServiceName}MiddlewareService
- **核心服務路徑**：`./src/app/main/core/api/services/invalid.service.ts`
- **處理狀態**：❌ 失敗
- **原因**：核心服務檔案不存在

### 統計
- 處理檔案數：{count}
- 新建檔案數：{count}
- 更新檔案數：{count}
- 跳過檔案數：{count}
- 失敗檔案數：{count}
- 新增方法總數：{count}
```

## 方法分類與處理規範

根據方法類型套用對應的處理模式：

### 1. 查詢方法 (get*, getAll*)
- 使用 `firstValueFrom()` 搭配 `.pipe(map(), catchError())`
- 列表查詢回傳陣列，使用 `of([])` 作為錯誤預設值
- 單一物件查詢回傳物件或 null，使用 `of(null)` 作為錯誤預設值
- catchError 中使用 console.error 記錄錯誤但不拋出

### 2. 異動方法 (post*, put*, delete*)
- 使用 `firstValueFrom()` 搭配 `.pipe(map(), catchError())`
- 使用 `map((result: ApiResult<T>) => result)` 直接返回 ApiResult
- catchError 中使用 console.error 記錄錯誤後 `throw error`
- 讓呼叫方處理業務邏輯

### 3. 特殊方法 (功能測試等)
- 依據實際業務需求決定處理方式
- 通常遵循查詢方法規範

### 4. 必須遵循的 RxJS 寫法

```typescript
// 查詢方法範例 (Offset 分頁，傳入 offset, limit 參數)
return firstValueFrom(
    this.coreService.getData({
        serviceUuid: this._serviceUuid,
        queryParams: { filter },
        path: {
            variables: { offset, limit },
            keys: { offset: '{offset}', limit: '{limit}' }
        }
    }).pipe(
        map((result: ApiResult<T>) => {
            return result.success && result.data ? result.data : null;
        }),
        catchError((error: ErrorResult) => {
            console.error('ServiceName method error:', error);
            return of(null);
        })
    )
);

// 異動方法範例 (POST/PUT body 放在 config 中)
return firstValueFrom(
    this.coreService.postData({
        serviceUuid: this._serviceUuid,
        queryParams: { filter },
        body: data
    }).pipe(
        map((result: ApiResult<T>) => result),
        catchError((error: ErrorResult) => {
            console.error('ServiceName method error:', error);
            throw error;
        })
    )
);
```

## 注意事項

### 配置檔案管理
- 嚴格遵循 `Middleware_CONFIG_FILE` 中的映射表設定
- 映射表更新後需要重新執行生成流程
- 核心服務路徑必須從 `.src/app` 開始

### 檔案比對邏輯
- 比對方法名稱而非完整簽名
- 只要方法名稱相同即視為已存在
- 🚫 **絕對不可修改既有方法的實作內容**

### 路徑處理
- **資料夾自動建立**：如果目標資料夾不存在，自動建立完整路徑
- **import 路徑修正**：自動調整模型檔案的 import 路徑
- **跨平台支援**：處理不同作業系統的路徑分隔符號

### 錯誤處理
- **配置檔案不存在**：立刻終止執行並提示錯誤
- **核心服務檔案不存在**：記錄錯誤並跳過該檔案，繼續處理其他檔案
- **路徑格式不符**：提示使用者核心服務路徑必須從 `.src/app` 開始
- **目標資料夾無法建立**：報錯並跳過該檔案
- **檔案無法解析**：記錄錯誤並繼續處理其他檔案
- **import 路徑無法解析**：使用預設路徑並記錄警告

### 最佳實踐
1. **明確配置映射表**：在配置檔案中清楚指定核心服務與目標資料夾的對應關係
2. **定期更新映射表**：新增服務時同步更新映射表
3. **驗證生成結果**：生成後檢查 import 路徑和方法實作是否正確
4. **保護既有程式碼**：🚫 **嚴格遵守不異動既有程式碼的規則**
5. **方法插入位置**：新方法必須插入在 `destroy()` 方法之前
6. **依賴注入規範**：禁止使用 constructor 注入 ，必須使用 Angular 的 `inject()` 函式進行注入
