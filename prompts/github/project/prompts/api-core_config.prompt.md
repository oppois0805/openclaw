---
name: "api-core_config"
description: "Core API 配置提示：用於產生 Core 層級的 API 服務配置檔案"
---

## API 列表
| HTTP方法 | API路徑 | 說明 | ResourceName |
|---------|---------|------|------------|
| GET | /v1/calculation-setting/indicators/{id}/limit/{limit}/list | 依起始 ID 和數量上限取得指標 (簡易資料) | Indicator |
| POST | /v1/system-update/server-software | Upload the server software update package. | SystemUpdate |
| GET | /v1/data-calculation/variable/{ids} | 依指定 ID 取得指標變數 | Variable |


### 檔案位置
```
Service 檔案 = "../../src/app/main/core/api/services/{resource-name}.service.ts"
Interface 檔案 = "../../src/app/main/core/api/dto/{resource-name}.dto.ts"
Enum 檔案 = "../../src/app/main/core/api/enums/{resource-name}.enum.ts"
```
