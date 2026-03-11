---
name: "api_doc-service"
description: "文件 API 服務提示：用於產生文件相關的 API Service 程式碼"
---

# 你的任務

根據使用者提供的設定變數，協助建立符合專案規範的 Service 使用文件。

## 設定變數

```bash
# Service 名稱 (首字母大寫)
SERVICE_NAME=${input:variableName:featureName}

# 檔案建立位置
FILE_PATH=${input:variableName:fileName}

# 工作區根目錄
WORKSPACE_FOLDER=${workspaceFolder}
```

## 使用規則

請依據上方設定變數寫出 {SERVICE_NAME} 的使用文件，在 {FILE_PATH} 建立一個md檔，寫入 {WORKSPACE_FOLDER} 相關開發說明文件。相關規則如下：
- 應將所有可能的傳入情境都列出，並標註每種情境的預期返回值，讓開發者一目了然。
- 區塊需格式化，讓每個範例都分行、易讀，並加上註解說明預期結果，符合開發指南的格式化標準。
- 路徑一律用絕對路徑，例如: 
```typescript
import { ChartUtilsService } from 'src/app/common/service/chart/chart-utils.service';
```
- 不需解釋。
- 不要自創範本沒有的項目，除非是必要的。interface 也需確實有該項目才寫入

## 開發步驟

### 步驟 1：建立文件檔案

在 {FILE_PATH} 位置建立 Markdown 文件檔案。

### 步驟 2：套用變數替換

將下方範本中的變數進行替換：

- `{SERVICE_NAME}` → 使用者設定的 SERVICE_NAME
- `{service_name}` → SERVICE_NAME 的 camelCase 格式
- `{WORKSPACE_FOLDER}` → 使用者設定的 WORKSPACE_FOLDER

### 步驟 3：內容驗證

確認所有介面、方法和屬性都確實存在於實際的服務中。

## 完整範本

# [{SERVICE_NAME}] Service Usage Documentation

## 1. 概述
[簡要描述此 Service 的主要功能和用途，1-2 句話即可]

## 2. 安裝與匯入

### 2.1. 依賴項目
```typescript
// 需要的外部套件
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
// 其他必要的依賴...
```

### 2.2. 註冊服務
```typescript
// 在 module 中註冊
providers: [
  [ServiceName]
]

// 或使用 Injectable 裝飾器
@Injectable()
```

### 2.3. 匯入使用
```typescript
import { {SERVICE_NAME}Service } from 'src/app/path/to/{service_name}.service';

constructor(private {service_name}Service: {SERVICE_NAME}Service) {}
```

## 3. API 參考

### 3.1. 屬性

| 屬性名稱 | 類型 | 預設值 | 說明 | 只讀 | 存取修飾符 |
|---------|------|--------|------|------|-----------|
| `propertyName` | `Type` | `defaultValue` | [屬性用途說明] | 是/否 | public |
| `configOptions` | `ConfigInterface` | `{}` | [設定選項物件] | 否 | private |
| `isLoading` | `boolean` | `false` | [載入狀態] | 是 | protected |

### 3.2. 方法

| 方法名稱 | 說明 | 存取修飾符 | 非同步 |
|---------|------|-----------|------|
| `methodName` | [方法功能詳細說明] | public | 否 |
| `validateData` | [驗證資料格式] | private | 否 |
| `resetState` | [重置服務狀態] | protected | 否 |
| `destroy` | [銷毀服務實例，清理資源] | public | 否 |

#### 3.2.1. methodName

[方法功能詳細說明]

**介面定義**
```typescript
methodName(param1: Type1, param2?: Type2): ReturnType
```

**參數說明**
| 參數名稱 | 類型 | 必填 | 說明 |
|---------|------|------|------|
| `param1` | `Type1` | 是 | [參數說明] |
| `param2` | `Type2` | 否 | [參數說明] |

**返回值**
| 類型 | 說明 |
|------|------|
| `ReturnType` | [返回值說明] |

**注意事項**
- 注意事項...

**範例**:
```typescript
// 基本用法
const result = this.{service_name}Service.methodName(value1, value2);

// 進階用法
this.{service_name}Service.methodName(complexParam).subscribe(
  (data) => {
    // 處理成功回應
  },
  (error) => {
    // 處理錯誤
  }
);
```

#### 3.2.2. validateData

[驗證資料格式]

**介面定義**
```typescript
validateData(data: any): ValidationResult
```

**參數說明**
| 參數名稱 | 類型 | 必填 | 說明 |
|---------|------|------|------|
| `data` | `any` | 是 | [參數說明] |

**返回值**
| 類型 | 說明 |
|------|------|
| `ValidationResult` | [返回值說明] |

**注意事項**
- 注意事項...

**範例**:
```typescript
const result = this.{service_name}Service.validateData(inputData);
if (result.isValid) {
  // 驗證通過
} else {
  // 處理驗證錯誤
  console.log('驗證錯誤:', result.errors);
}
```

#### 3.2.3 resetState

[重置服務狀態]

**介面定義**
```typescript
resetState(): void
```

**參數說明**
| 參數名稱 | 類型 | 必填 | 說明 |
|---------|------|------|------|
| - | - | - | 無參數 |

**返回值**
| 類型 | 說明 |
|------|------|
| `void` | 無返回值 |

**注意事項**
- 注意事項...

**範例**:
```typescript
this.{service_name}Service.resetState();
```

#### 3.2.4. destroy

[銷毀服務實例，清理資源]

**介面定義**
```typescript
destroy(): void
```

**參數說明**
| 參數名稱 | 類型 | 必填 | 說明 |
|---------|------|------|------|
| - | - | - | 無參數 |

**返回值**
| 類型 | 說明 |
|------|------|
| `void` | 無返回值 |

**注意事項**
- 注意事項...

**範例**:
```typescript
// 在元件銷毀時調用
ngOnDestroy() {
  this.{service_name}Service.destroy();
}
```

## 4. 介面定義

### 4.1. 核心介面

```typescript
// 主要資料結構
interface [MainDataInterface] {
  id: string;
  name: string;
  status: StatusType;
  createdAt: Date;
  updatedAt?: Date;
}

// 設定選項介面
interface [ConfigInterface] {
  option1: string;
  option2?: number;
  option3: boolean;
}

// 驗證結果介面
interface ValidationResult {
  isValid: boolean;
  errors?: string[];
  warnings?: string[];
}

// 查詢參數介面
interface QueryParams {
  page?: number;
  limit?: number;
  sortBy?: string;
  sortOrder?: 'asc' | 'desc';
}
```

### 4.2. 回應介面

```typescript
// API 回應格式
interface ApiResponse<T> {
  success: boolean;
  data: T;
  message?: string;
  timestamp: Date;
}

// 分頁回應格式
interface PaginatedResponse<T> {
  items: T[];
  total: number;
  page: number;
  limit: number;
  hasNext: boolean;
  hasPrev: boolean;
}
```

### 4.3. 列舉定義

```typescript
// 狀態類型
enum StatusType {
  ACTIVE = 'active',
  INACTIVE = 'inactive',
  PENDING = 'pending',
  ARCHIVED = 'archived'
}

// 錯誤類型
enum ErrorType {
  VALIDATION_ERROR = 'validation_error',
  NETWORK_ERROR = 'network_error',
  TIMEOUT_ERROR = 'timeout_error',
  UNKNOWN_ERROR = 'unknown_error'
}
```

## 5. 注意事項

- [重要提醒 1]
- [重要提醒 2]
- [性能考量]
- [記憶體管理建議]

## 6. 更新日誌

| 版本 | 日期 | 變更內容 |
|------|------|---------|
| v1.0.0 | [日期] | - [功能描述]<br>- [變更內容] |
| v1.1.0 | [日期] | - [新增功能]<br>- [修正問題] |
| v1.2.0 | [日期] | - [效能優化]<br>- [API 調整] |
