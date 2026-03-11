---
name: confluence-query
description: "Confluence Jira Bug 查詢：當需要從 Confluence 頁面中查詢 Jira Bug 單號時使用。透過 Confluence MCP 工具讀取使用者提供的 Confluence 頁面網址，解析頁面內容中的 Jira 單號（SWD-xxx、QA-xxx、SW-xxx 等格式）。適用場景：Bug 修復前查詢 Jira 單號、確認 Bug 描述、取得 Bug 詳細資訊。"
---

# Confluence Jira Bug 查詢

透過 Confluence MCP 查詢頁面中的 Jira Bug 單號與相關資訊。

## 前置條件

- 使用者必須提供 Confluence 頁面網址
- Confluence MCP 連線設定必須正確

---

## 查詢流程

```
1️⃣ 取得使用者提供的 Confluence 頁面 URL
    ↓
2️⃣ 從 URL 中解析 Page ID 或搜尋關鍵字
    ↓
3️⃣ 使用 Confluence MCP 讀取頁面內容
    ↓
4️⃣ 解析頁面中的 Jira 單號
    ↓
5️⃣ 列出查詢結果供使用者確認
```

---

## URL 解析規則

### Confluence Cloud URL 格式

```
https://<domain>.atlassian.net/wiki/spaces/<SPACE>/pages/<PAGE_ID>/<page-title>
```

- 從 URL 中提取 `PAGE_ID`（純數字）

### Confluence Server URL 格式

```
https://<domain>/confluence/pages/viewpage.action?pageId=<PAGE_ID>
https://<domain>/confluence/display/<SPACE>/<page-title>
```

---

## MCP 操作指引

### 方式一：透過 Page ID 直接讀取

```
Tool: mcp_confluence_conf_get
path: /wiki/api/v2/pages/<PAGE_ID>
queryParams: { "body-format": "view" }
jq: "{id: id, title: title, body: body}"
```

### 方式二：透過搜尋查詢

```
Tool: mcp_confluence_conf_get
path: /wiki/rest/api/search
queryParams: { "cql": "type=page AND title~\"<keyword>\"", "limit": "5" }
jq: "results[*].{id: content.id, title: content.title}"
```

### 方式三：透過頁面取得 Body 內容

```
Tool: mcp_confluence_conf_get
path: /wiki/api/v2/pages/<PAGE_ID>/body
queryParams: { "body-format": "view" }
```

---

## Jira 單號解析規則

### 支援的 Jira ID 格式

| Pattern              | 說明                    | 範例       |
| :------------------- | :---------------------- | :--------- |
| `SWD-\d+`           | Software Development    | SWD-42     |
| `QA-\d+`            | Quality Assurance       | QA-15      |
| `SW-\d+`            | Software                | SW-8       |
| `[A-Z]+-\d+`        | 其他 Jira 專案 ID       | PROJ-123   |

### 解析策略

1. 使用正則表達式 `[A-Z]{2,10}-\d+` 掃描頁面內容
2. 去除重複的單號
3. 依據單號前綴分組

---

## 查詢結果格式

查詢完成後，以下列格式呈現結果：

```markdown
## 📋 Confluence Bug 查詢結果

| #  | Jira 單號  | Bug 標題 / 描述摘要          | 狀態     |
| :- | :--------- | :--------------------------- | :------- |
| 1  | SWD-42     | Login page crash on submit   | Open     |
| 2  | QA-15      | Form validation not working  | Open     |

**來源頁面**：<Page Title>
**查詢時間**：<Timestamp>
```

---

## 錯誤處理

| 錯誤情境               | 處理方式                           |
| :--------------------- | :--------------------------------- |
| URL 格式無效           | 提示使用者提供正確的 Confluence URL |
| 頁面不存在或無權限     | 回報錯誤，請使用者確認權限          |
| 頁面中無 Jira 單號     | 回報未找到，詢問是否手動輸入單號    |
| MCP 連線失敗           | 提示使用者檢查 MCP 設定            |

---

## 🔴 禁止事項

- 🔴 未經使用者確認就使用查詢到的 Jira 單號
- 🔴 自行猜測 Jira 單號
- 🔴 忽略頁面讀取錯誤繼續執行
```
