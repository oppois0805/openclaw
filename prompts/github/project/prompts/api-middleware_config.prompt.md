---
name: "api-middleware_config"
description: "Middleware API 配置提示：用於產生 Middleware 層級的 API 服務配置檔案"
---

## 📋 核心服務與目標資料夾映射表

| 核心服務檔案路徑 (從 ./src/app 開始) | 目標資料夾路徑 | 說明 |
|-----------------------------------|--------------|------|
| ../../src/app/main/core/api/services/indicator.service.ts | ../../src/app/main/features/ip-address | 設備管理服務 |
| ../.../../src/app/main/core/api/services/systemUpdate.service.ts | ../../src/app/main | IO設定服務 |
| ../../src/app/main/core/api/services/variable.service.ts | ../../src/app/main/features/serial-port | 系統變數服務 |
