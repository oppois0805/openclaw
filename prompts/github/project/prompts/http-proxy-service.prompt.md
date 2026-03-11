---
name: "http-proxy-service"
description: "HTTP Proxy 服務提示：用於產生 HTTP Proxy 相關的 Service 程式碼"
---

# [HttpProxyService] Service Usage Documentation

## 1. 概述
HttpProxyService 是一個統一的 HTTP 請求代理服務，提供快取管理、錯誤處理和多種 API 請求方法，支援 GET、POST、PUT、DELETE 等操作。

# Prompt 使用方法

## api-core_service.md 使用說明
- 加入三個檔案 api-core_service.md、api-core_config.md 和 swagger.json( {ip}/api/swagger/v1/swagger.json )
- api-core_service.md 修改主要參考檔案路徑
- api-core_config.md 裡面新增 API 路徑
- 依據 api-core_service.md 執行 Agent 產生服務檔案

## api-middleware_service.md 使用說明
- 加入兩個檔案 api-middleware_service.md、api-middleware_config.md
- api-middleware_service.md 修改主要參考檔案路徑
- api-middleware_config.md 裡面新增 API 路徑
- 依據 api-middleware_service.md 執行 Agent 產生服務檔案



## 2. 安裝與匯入

### 2.1. 依賴項目
```typescript
// 需要的外部套件
import { Injectable } from '@angular/core';
import { HttpClient, HttpErrorResponse } from '@angular/common/http';
import { Observable, Subject, of, firstValueFrom } from 'rxjs';
import { takeUntil, switchMap, map, shareReplay, finalize, catchError } from 'rxjs/operators';
import { ApiListResult, ApiResult } from 'shared/models/http/common.model';
```

### 2.2. 註冊服務
```typescript
@Injectable()
```

### 2.3. 匯入使用
```typescript
import { HttpProxyService } from 'shared/services/http/http-proxy.service';

constructor(private httpProxyService: HttpProxyService) {}
```

## 3. API 參考

### 3.1. 屬性

| 屬性名稱 | 類型 | 預設值 | 說明 | 只讀 | 存取修飾符 |
|---------|------|--------|------|------|-----------|
| `apiCacheKeyToRequestCacheMap` | `Map<string, Observable<any>>` | `new Map()` | 快取鍵到請求 Observable 的映射 | 否 | private |
| `serviceUuidToApiCacheKeyMap` | `Map<string, Set<string>>` | `new Map()` | 服務唯一識別碼到快取鍵的映射 | 否 | private |
| `apiCacheKeyToCancelSubjectsMap` | `Map<string, Subject<void>>` | `new Map()` | 每個快取鍵對應的取消 Subject | 否 | private |
| `offset` | `number` | `0` | 預設偏移量 | 是 | private |
| `limit` | `number` | `50` | 預設限制數量 | 是 | private |

### 3.2. 方法

| 方法名稱 | 說明 | 存取修飾符 | 非同步 |
|---------|------|-----------|------|
| `clearCacheByServiceUuid` | 清除特定服務 UUID 的快取 | public | 否 |
| `getListByOffset` | 依據偏移量請求列表 | public | 是 |
| `getAllListByOffset` | 一次取得所有資料（自動分頁） | public | 是 |
| `getResource` | 通用的 API 資源請求處理器 | public | 是 |
| `postResource` | POST 資源請求處理器 | public | 是 |
| `putResource` | PUT 資源請求處理器 | public | 是 |
| `deleteResource` | DELETE 資源請求處理器 | public | 是 |

#### 3.2.1. clearCacheByServiceUuid

清除特定服務 UUID 的快取

**介面定義**
```typescript
clearCacheByServiceUuid(serviceUuid: string): void
```

**參數說明**
| 參數名稱 | 類型 | 必填 | 說明 |
|---------|------|------|------|
| `serviceUuid` | `string` | 是 | 服務唯一識別碼 |

**返回值**
| 類型 | 說明 |
|------|------|
| `void` | 無返回值 |

**注意事項**
- 只會清除指定 serviceUuid 的快取
- 如果快取被多個服務共用，只有當沒有其他服務使用時才會實際清除

**範例**:
```typescript
// 清除特定服務的快取
this.httpProxyService.clearCacheByServiceUuid('user-service-abc123');

// 在服務銷毀時清除快取
ngOnDestroy() {
  this.httpProxyService.clearCacheByServiceUuid(this._serviceUuid);
}
```

#### 3.2.2. getListByOffset

依據偏移量請求列表資料

**介面定義**
```typescript
getListByOffset<T>(
    urlTemplate: string,
    urlParams: Record<string, any>,
    serviceUuid: string,
    filter?: Record<string, any> | undefined | null,
    headers?: Record<string, string> | undefined | null,
    useCache: boolean = false
): Observable<ApiListResult<T>>
```

**參數說明**
| 參數名稱 | 類型 | 必填 | 說明 |
|---------|------|------|------|
| `urlTemplate` | `string` | 是 | API URL 模板，例如: `/api/device/offset/{offset}/limit/{limit}/list` |
| `urlParams` | `Record<string, any>` | 是 | URL 參數物件，例如: `{'{offset}': 0, '{limit}': 50}` |
| `serviceUuid` | `string` | 是 | 服務唯一識別碼，用於區分不同 Service 的快取 |
| `filter` | `Record<string, any>` | 否 | 篩選條件 |
| `headers` | `Record<string, string>` | 否 | HTTP 請求標頭 |
| `useCache` | `boolean` | 否 | 是否使用快取，預設為 false |

**返回值**
| 類型 | 說明 |
|------|------|
| `Observable<ApiListResult<T>>` | 包含列表資料的 API 回應 Observable |

**注意事項**
- URL 模板中的參數會被 urlParams 中的值替換
- 啟用快取時會自動管理快取生命週期
- 適用於分頁查詢場景

**範例**:
```typescript
// 基本用法 - 不使用快取
const result = await firstValueFrom(
  this.httpProxyService.getListByOffset(
    '/api/users/offset/{offset}/limit/{limit}/list',
    { '{offset}': 0, '{limit}': 20 },
    'user-service-abc123'
  )
);
// API 送出結果: '/api/users/offset/0/limit/20/list'

// 進階用法 - 使用快取和篩選條件
const result = await firstValueFrom(
  this.httpProxyService.getListByOffset(
    '/api/users/offset/{offset}/limit/{limit}/list',
    { '{offset}': 0, '{limit}': 20 },
    'user-service-abc123',
  { status: 'active', role: 'admin' },  // 篩選條件
  { 'Authorization': 'Bearer token' },  // 請求標頭
  true  // 啟用快取
);
// API 送出結果: '/api/users/offset/0/limit/20/list?filter={"status":"active","role":"admin"}'

// 處理回應資料
if (result.success) {
  console.log('總筆數:', result.data.total);
  console.log('列表資料:', result.data.result);
} else {
  console.error('請求失敗:', result.message);
}
```

#### 3.2.3. getAllListByOffset

一次取得所有資料，自動處理分頁

**介面定義**
```typescript
getAllListByOffset<T>(
    urlTemplate: string,
    limit: number,
    serviceUuid: string,
    filter?: Record<string, any> | undefined | null,
    urlParams?: Record<string, any> | undefined | null,
    headers?: Record<string, string> | undefined | null,
    offsetParamName: string = '{offset}',
    limitParamName: string = '{limit}',
    useCache: boolean = false
): Observable<ApiListResult<T>>
```

**參數說明**
| 參數名稱 | 類型 | 必填 | 說明 |
|---------|------|------|------|
| `urlTemplate` | `string` | 是 | API URL 模板 |
| `limit` | `number` | 是 | 每次請求的數量上限 |
| `serviceUuid` | `string` | 是 | 服務唯一識別碼 |
| `filter` | `Record<string, any>` | 否 | 篩選條件 |
| `urlParams` | `Record<string, any>` | 否 | 其他 URL 參數，不需要包含 offset 和 limit，例如: `{'{category}': 'A'}` |
| `headers` | `Record<string, string>` | 否 | HTTP 請求標頭 |
| `offsetParamName` | `string` | 否 | 偏移量參數名稱，預設為 '{offset}' |
| `limitParamName` | `string` | 否 | 限制數量參數名稱，預設為 '{limit}' |
| `useCache` | `boolean` | 否 | 是否使用快取，預設為 false |

**返回值**
| 類型 | 說明 |
|------|------|
| `Observable<ApiListResult<T>>` | 包含所有資料的 API 回應 Observable |

**注意事項**
- 會自動處理分頁，遞迴請求直到取得所有資料
- 適用於需要取得完整資料集的場景
- 大量資料時請謹慎使用，考慮記憶體使用量

**範例**:
```typescript
// 基本用法 - 取得所有使用者資料
const allUsers = await firstValueFrom(
  this.httpProxyService.getAllListByOffset(
    '/api/users/offset/{offset}/limit/{limit}/list',
    50,  // 每次請求 50 筆
    'user-service-abc123'
  )
);
// API 送出結果: '/api/users/offset/0/limit/50/list', '/api/users/offset/50/limit/50/list', ...

// 進階用法 - 自訂參數名稱
const allDevices = await firstValueFrom(
  this.httpProxyService.getAllListByOffset(
  '/api/projects/{projectId}/devices/start/{start}/count/{count}/list',
  100,
  'device-service-xyz789',
  { category: 'sensor' },  // 篩選條件
  { '{projectId}': 'proj-123' },  // 額外 URL 參數
  null,  // 無額外標頭
  '{start}',  // 自訂偏移量參數名稱
  '{count}',  // 自訂限制數量參數名稱
  true  // 啟用快取
);
// API 送出結果: '/api/projects/proj-123/devices/start/0/count/100/list?filter={"category":"sensor"}', '/api/projects/proj-123/devices/start/100/count/100/list?filter={"category":"sensor"}', ...

console.log('取得所有資料:', allDevices.data.result);
```

#### 3.2.4. getResource

通用的單一資源請求處理器

**介面定義**
```typescript
getResource<T>(
    urlTemplate: string,
    serviceUuid: string,
    urlParams?: Record<string, any> | undefined | null,
    filter?: Record<string, any> | undefined | null,
    headers?: Record<string, string> | undefined | null,
    useCache: boolean = false
): Observable<ApiResult<T>>
```

**參數說明**
| 參數名稱 | 類型 | 必填 | 說明 |
|---------|------|------|------|
| `urlTemplate` | `string` | 是 | API URL 模板 |
| `serviceUuid` | `string` | 是 | 服務唯一識別碼 |
| `urlParams` | `Record<string, any>` | 否 | URL 參數物件 |
| `filter` | `Record<string, any>` | 否 | 篩選條件 |
| `headers` | `Record<string, string>` | 否 | HTTP 請求標頭 |
| `useCache` | `boolean` | 否 | 是否使用快取，預設為 false |

**返回值**
| 類型 | 說明 |
|------|------|
| `Observable<ApiResult<T>>` | 包含單一資源的 API 回應 Observable |

**注意事項**
- 適用於 GET 單一資源的場景
- 支援 URL 參數替換和查詢參數

**範例**:
```typescript
// 取得特定使用者資料
const user = await firstValueFrom(
  this.httpProxyService.getResource(
    '/api/users/{userId}',
    'user-service-abc123',
    { '{userId}': '12345' }
  )
);
// API 送出結果: '/api/users/12345'

// 取得系統狀態（無參數）
const status = await firstValueFrom(
  this.httpProxyService.getResource(
    '/api/system/status',
    'system-service-def456'
  )
);
// API 送出結果: '/api/system/status'

// 使用篩選條件和快取
const deviceInfo = await firstValueFrom(
  this.httpProxyService.getResource(
    '/api/devices/{deviceId}/info',
    'device-service-xyz789',
    { '{deviceId}': 'device-001' },
    { includeStats: true },  // 篩選條件
    { 'Accept': 'application/json' },  // 請求標頭
    true  // 啟用快取
  )
);
// API 送出結果: '/api/devices/device-001/info?filter={"includeStats":true}'
```

#### 3.2.5. postResource

POST 資源請求處理器

**介面定義**
```typescript
postResource<T>(
    urlTemplate: string,
    body: any,
    urlParams?: Record<string, any> | undefined | null,
    filter?: Record<string, any> | undefined | null,
    headers?: Record<string, string> | undefined | null
): Observable<ApiResult<T>>
```

**參數說明**
| 參數名稱 | 類型 | 必填 | 說明 |
|---------|------|------|------|
| `urlTemplate` | `string` | 是 | API URL 模板 |
| `body` | `any` | 是 | 請求主體資料 |
| `urlParams` | `Record<string, any>` | 否 | URL 參數物件 |
| `filter` | `Record<string, any>` | 否 | 篩選條件 |
| `headers` | `Record<string, string>` | 否 | HTTP 請求標頭 |

**返回值**
| 類型 | 說明 |
|------|------|
| `Observable<ApiResult<T>>` | API 回應結果 Observable |

**注意事項**
- POST 請求不支援快取機制
- 適用於建立新資源的場景

**範例**:
```typescript
// 建立新使用者
const newUser = {
  name: 'John Doe',
  email: 'john@example.com',
  role: 'user'
};

const result = await firstValueFrom(
  this.httpProxyService.postResource(
    '/api/users',
    newUser
  )
);
// API 送出結果: 'POST /api/users' (body: newUser)

// 建立裝置配置
const deviceConfig = {
  name: 'Sensor-001',
  type: 'temperature',
  location: 'Room A'
};

const createResult = await firstValueFrom(
  this.httpProxyService.postResource(
  '/api/projects/{projectId}/devices',
  deviceConfig,
  { '{projectId}': 'proj-123' },  // URL 參數
  { autoStart: true },  // 篩選條件
  { 'Content-Type': 'application/json' }  // 請求標頭
);
// API 送出結果: 'POST /api/projects/proj-123/devices?filter={"autoStart":true}' (body: deviceConfig)
```

#### 3.2.6. putResource

PUT 資源請求處理器

**介面定義**
```typescript
putResource<T>(
    urlTemplate: string,
    body: any,
    urlParams?: Record<string, any> | undefined | null,
    filter?: Record<string, any> | undefined | null,
    headers?: Record<string, string> | undefined | null
): Observable<ApiResult<T>>
```

**參數說明**
| 參數名稱 | 類型 | 必填 | 說明 |
|---------|------|------|------|
| `urlTemplate` | `string` | 是 | API URL 模板 |
| `body` | `any` | 是 | 請求主體資料 |
| `urlParams` | `Record<string, any>` | 否 | URL 參數物件 |
| `filter` | `Record<string, any>` | 否 | 篩選條件 |
| `headers` | `Record<string, string>` | 否 | HTTP 請求標頭 |

**返回值**
| 類型 | 說明 |
|------|------|
| `Observable<ApiResult<T>>` | API 回應結果 Observable |

**注意事項**
- PUT 請求不支援快取機制
- 適用於更新現有資源的場景

**範例**:
```typescript
// 更新使用者資料
const updatedUser = {
  name: 'John Smith',
  email: 'john.smith@example.com',
  role: 'admin'
};

const result = await firstValueFrom(
  this.httpProxyService.putResource(
    '/api/users/{userId}',
    updatedUser,
    { '{userId}': '12345' }
  )
);
// API 送出結果: 'PUT /api/users/12345' (body: updatedUser)

// 更新裝置設定
const deviceSettings = {
  name: 'Updated Sensor',
  enabled: true,
  interval: 30
};

const updateResult = await firstValueFrom(
  this.httpProxyService.putResource(
    '/api/devices/{deviceId}/settings',
    deviceSettings,
    { '{deviceId}': 'device-001' },
    { forceUpdate: true },  // 篩選條件
    { 'Content-Type': 'application/json' }
  )
);
// API 送出結果: 'PUT /api/devices/device-001/settings?filter={"forceUpdate":true}' (body: deviceSettings)
```

#### 3.2.7. deleteResource

DELETE 資源請求處理器

**介面定義**
```typescript
deleteResource<T>(
    urlTemplate: string,
    urlParams?: Record<string, any> | undefined | null,
    filter?: Record<string, any> | undefined | null,
    headers?: Record<string, string> | undefined | null
): Observable<ApiResult<T>>
```

**參數說明**
| 參數名稱 | 類型 | 必填 | 說明 |
|---------|------|------|------|
| `urlTemplate` | `string` | 是 | API URL 模板 |
| `urlParams` | `Record<string, any>` | 否 | URL 參數物件 |
| `filter` | `Record<string, any>` | 否 | 篩選條件 |
| `headers` | `Record<string, string>` | 否 | HTTP 請求標頭 |

**返回值**
| 類型 | 說明 |
|------|------|
| `Observable<ApiResult<T>>` | API 回應結果 Observable |

**注意事項**
- DELETE 請求不支援快取機制
- 適用於刪除資源的場景

**範例**:
```typescript
// 刪除使用者
const result = await firstValueFrom(
  this.httpProxyService.deleteResource(
    '/api/users/{userId}',
    { '{userId}': '12345' }
  )
);
// API 送出結果: 'DELETE /api/users/12345'

// 刪除裝置
const deleteResult = await firstValueFrom(
  this.httpProxyService.deleteResource(
    '/api/devices/{deviceId}',
    { '{deviceId}': 'device-001' },
    null,
    { 'Authorization': 'Bearer token' }
  )
);
// API 送出結果: 'DELETE /api/devices/device-001'

// 批量刪除
const batchDeleteResult = await firstValueFrom(
  this.httpProxyService.deleteResource(
    '/api/users/batch',
    null,
    { ids: '1,2,3,4,5' }  // 透過篩選條件傳遞 ID 列表
  )
);
// API 送出結果: 'DELETE /api/users/batch?filter={"ids":"1,2,3,4,5"}'
```

## 4. 介面定義

### 4.1. 核心介面

```typescript
// API 列表回應格式
interface ApiListResult<T> {
  code: string | null;
  message: string | null;
  developerMessage: string | null;
  data: {
    result: T[];
    total: number;
  };
  success: boolean;
  timestamp: string;
}

// API 單一資源回應格式
interface ApiResult<T> {
  code: string | null;
  message: string | null;
  developerMessage: string | null;
  data: T;
  success: boolean;
  timestamp: string;
}

// 錯誤回應格式
interface ErrorResult {
  code: string | null;
  message: string | null;
  developerMessage: string | null;
  data: StateCodeDataResult[];
  success: boolean;
  timestamp: string;
}

// 狀態碼資料結構
interface StateCodeDataResult {
  stateCode: string;
  data: any[];
  additionalProperties: string | null;
}
```

## 5. 注意事項

- **快取管理**: 服務提供自動快取管理機制，建議在適當時機啟用快取以提升效能
- **記憶體管理**: 使用 `getAllListByOffset` 時要注意資料量大小，避免記憶體溢出
- **錯誤處理**: 服務內建統一錯誤處理機制，無需在業務層重複處理
- **並發控制**: 相同的請求會自動共享 Observable，避免重複請求
- **生命週期管理**: 務必在服務銷毀時調用 `clearCacheByServiceUuid` 清除快取
- **URL 模板**: URL 模板中的參數必須與 urlParams 中的鍵值對應
- **serviceUuid 唯一性**: 每個服務實例應使用唯一的 serviceUuid 以避免快取衝突

## 6. 更新日誌

| 版本 | 日期 | 變更內容 |
|------|------|---------|
| v1.0.0 | 2025/09/18 | - 初始版本<br>- 支援基本 CRUD 操作<br>- 快取機制實作 |
| v1.1.0 | 2025/09/18 | - 參數名稱變更：cachePrefix → serviceUuid<br>- 方法名稱變更：clearCacheByPrefix → clearCacheByServiceUuid<br>- 改善快取鍵映射機制 |
| v1.2.0 | 2025/09/23 | - 重構：所有 public 方法從 Promise 改為 Observable<br>- 更新所有使用範例，使用 firstValueFrom 包裝<br>- 修正文檔中的參數格式說明<br>- 新增所有範例的 API 結果註解 |
