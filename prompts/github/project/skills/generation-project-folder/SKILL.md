---
name: generation-project-folder
description: "專案檔案產生技能：依據目錄規範產生 Angular 專案的檔案結構，包含 Component、Service、Store 等檔案的建立位置與命名規則。"
---

# Project 檔案產生

建立 Application 專案的 Angular 檔案時，依優先順序選擇工具。

## 決策流程

```
1️⃣ Angular MCP 可用？ ─YES→ ✅ 使用 MCP 工具
    │NO
    ▼
2️⃣ Angular CLI 可用？ ─YES→ ✅ 使用 CLI（先查詢最新語法）
    │NO
    ▼
3️⃣ 查詢最新規範後手動建立
```

## 參考規範

執行此技能時需理解以下規範：

| 規範                                                                                      | 說明                     |
| :---------------------------------------------------------------------------------------- | :----------------------- |
| [folder-system_application.md](../../rules/folder-system/folder-system_application.md) | Application 專案目錄結構 |
| [folder-system_api.md](../../rules/folder-system/folder-system_api.md)                 | API 三層架構目錄配置     |
| [angular-resources.md](references/angular-resources.md)                                   | Angular 官方資源連結     |

## 目錄結構概覽

```
src/app/
├── core/           # 核心功能（auth、config、api、guards）
├── features/       # 功能模組（支援遞迴巢狀）
├── shared/         # 共用元件（components、directives、pipes）
└── main.component.*
```

## Angular 資源

執行前**必須**查詢最新語法：

- **Angular LLM 文件**：https://angular.dev/llms.txt
- **Angular CLI 文件**：https://angular.dev/cli/generate

## 禁止事項

- 🔴 未檢查 MCP/CLI 前直接手動建立
- 🔴 使用記憶中的規範而非查詢最新文件
