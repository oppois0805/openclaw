# Library 檔案產生

建立 Library 元件庫的 Angular 檔案時，依優先順序選擇工具。

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

| 規範                                                                              | 說明                 |
| :-------------------------------------------------------------------------------- | :------------------- |
| [folder-system_library.md](../../rules/folder-system/folder-system_library.md) | Library 專案目錄結構 |
| [angular-resources.md](references/angular-resources.md)                           | Angular 官方資源連結 |

## 目錄結構概覽

```
src/lib/
├─ {library-name}.component.*   # 📌 主元件
├─ testing/                     # 🧪 測試假資料
├─ enums/                       # 🏷️ 列舉定義
├─ storybook/                   # 🔧 Storybook
├─ models/                      # 📋 模型/介面
├─ index.ts                     # 🔄 barrel（內部使用）
└─ public-api.ts                # 🌐 唯一對外 API
```

## Library 特有規則

| 規則            | 說明                                  |
| :-------------- | :------------------------------------ |
| 入口            | `src/lib`                             |
| 對外匯出        | 僅透過 `public-api.ts`                |
| 主元件          | 必須建立 `{library-name}.component.*` |
| 🚫 避免多餘組件 | 優先整合功能到主組件                  |

## Angular 資源

執行前**必須**查詢最新語法：

- **Angular LLM 文件**：https://angular.dev/llms.txt
- **Angular CLI 文件**：https://angular.dev/cli/generate

## 禁止事項

- 🔴 未檢查 MCP/CLI 前直接手動建立
- 🔴 使用記憶中的規範而非查詢最新文件
- 🔴 建立 `components/` 子目錄
- 🔴 建立包裝器組件（如 `-detail`、`-wrapper`）
