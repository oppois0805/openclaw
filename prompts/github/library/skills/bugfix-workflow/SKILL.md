---
name: bugfix-workflow
description: "Bugfix 分支建立與修復流程：當收到 Bug 回報需要建立修復分支、進行修復並產出回報時使用。包含分支命名規範（bugfix/xxxxx）、Git 分支操作流程、修復驗證檢查清單、標準化回報格式。適用場景：Bug 修復、Hotfix、緊急修正。"
---

# Bugfix 分支與修復流程

定義 Bug 修復的分支管理、修復流程與回報規範。

## 分支命名規範

```
bugfix/<jira-id>-<short-description>
```

### 範例

```
bugfix/SWD-42-fix-login-crash
bugfix/QA-15-form-validation
bugfix/SW-8-auth-error-handling
```

### 規則

- 前綴必須為 `bugfix/`
- 包含 Jira 單號（小寫）
- 使用 kebab-case 描述
- 描述簡潔明瞭

---

## 分支建立流程

```
1️⃣ 確認目前分支狀態（無未提交變更）
    ↓
2️⃣ 切換至基底分支（通常為 develop 或 main）
    ↓
3️⃣ 拉取最新程式碼（git pull）
    ↓
4️⃣ 建立並切換至 bugfix 分支
    ↓
5️⃣ 確認分支建立成功
```

### Git 指令範例

```bash
# 確認工作區乾淨
git status

# 切換至基底分支並更新
git checkout develop
git pull origin develop

# 建立 bugfix 分支
git checkout -b bugfix/<branch-name>

# 確認目前分支
git branch --show-current
```

---

## 修復流程

```
1️⃣ 閱讀 Bug 描述與重現步驟
    ↓
2️⃣ 定位問題程式碼
    ↓
3️⃣ 分析根本原因（Root Cause）
    ↓
4️⃣ 制定修復方案
    ↓
5️⃣ 實作修復
    ↓
6️⃣ 驗證修復（測試、編譯檢查）
    ↓
7️⃣ 提交修復（遵循 Commit 規範）
```

---

## 修復驗證檢查清單

- [ ] Bug 已無法重現
- [ ] 無 TypeScript 編譯錯誤
- [ ] 無 Console 錯誤
- [ ] 未影響其他功能
- [ ] import 路徑正確
- [ ] 修復邏輯簡潔清晰（無 hack）

---

## 回報格式

修復完成後，依以下格式產出回報：

```markdown
## 🐛 Bug 修復回報

### 基本資訊

| 項目         | 內容                          |
| :----------- | :---------------------------- |
| Jira 單號    | <JIRA-ID>                     |
| Bug 標題     | <Bug title from Jira>         |
| 修復分支     | bugfix/<branch-name>          |
| 影響範圍     | <Affected modules/components> |

### 根本原因

<Root cause analysis>

### 修復內容

1. <Change 1 description>
2. <Change 2 description>

### 異動檔案

| 檔案路徑       | 異動類型     | 說明           |
| :------------- | :----------- | :------------- |
| <file path>    | 修改/新增/刪除 | <description> |

### 驗證結果

- [ ] 編譯通過
- [ ] Bug 無法重現
- [ ] 未影響其他功能
- [ ] 程式碼審查通過

### Commit 記錄

\`\`\`
<JIRA-ID> fix: 🐛 <commit message>
\`\`\`
```

---

## 🔴 禁止事項

- 🔴 未確認基底分支就建立 bugfix 分支
- 🔴 分支命名不符合 `bugfix/` 前綴規範
- 🔴 修復未經驗證就提交
- 🔴 一個分支修復多個不相關的 Bug
- 🔴 跳過根本原因分析直接修改程式碼
```
