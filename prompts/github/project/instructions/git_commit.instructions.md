### 🧠 Git Commit 訊息規範

本規範定義標準化的 Commit 訊息格式，適用於所有與 Jira 連動的 GitLab 專案。

---

## 📋 Commit 類型與 Emoji 對照

| Type        | Emoji | 說明                                                      |
| :---------- | :---- | :-------------------------------------------------------- |
| `fix:`      | 🐛    | A bug fix                                                 |
| `feat:`     | 🎸    | A new feature                                             |
| `chore:`    | 🤖    | Build process or auxiliary tool changes                    |
| `ci:`       | 🎡    | CI related changes                                        |
| `docs:`     | ✏️    | Documentation only changes                                |
| `perf:`     | ⚡️    | A code change that improves performance                   |
| `refactor:` | 💡    | A code change that neither fixes a bug or adds a feature  |
| `style:`    | 💄    | Markup, white-space, formatting, missing semi-colons...   |
| `test:`     | 💍    | Adding missing tests                                      |

---

## 📋 Commit 訊息格式

### 單行格式

```
<JIRA-ID> <TYPE> <EMOJI> <SUMMARY>
```

### 多行格式（有詳細說明時）

```
<JIRA-ID> <TYPE> <EMOJI> <SUMMARY>
1. <Detail line 1>
2. <Detail line 2>
...etc.
```

### 多單號合併格式

當一次 Commit 同時修復多個相關的 Jira 單號時，使用底線 `_` 串接同前綴的單號數字：

```
<PREFIX>-<ID1>_<ID2>_<ID3> <TYPE> <EMOJI> <SUMMARY>
```

- 只保留第一個完整的 Jira ID，後續僅列數字部分並以 `_` 連接
- 僅適用於**同一前綴**的 Jira 單號（例如皆為 `QA-`）
- 若前綴不同，則分開為多個 Commit

---

## 📋 Commit 必要資訊

1. **Jira 單號**：必須包含有效的 Jira ID（例如：SWD-1、QA-3、SW-21），多個同前綴單號可用 `_` 合併
2. **變更類型**：從上方表格選取對應的 Type + Emoji
3. **簡短摘要**：一句話說明變更內容
4. **詳細說明**（可選）：列出具體修改項目

✅ `<TYPE>` 必須包含 Emoji，且在最終 Commit 訊息中完整保留。

⛔ Jira ID 必須有效（例如以 SWD-、QA-、SW- 開頭並接數字），無效則不得提交。

---

## ✅ Commit 範例

### 單行 commit：

```
SWD-1 feat: 🎸 Add login button UI
```

```
SWD-42 fix: 🐛 Fix null pointer on user logout
```

### 多單號合併 commit：

```
QA-4049_4160_4036 fix: 🐛 token 過期與跨分頁登出導致 404 及無法重導登入頁
```

```
SWD-10_23_45 chore: 🤖 Update shared config for affected modules
```

### 多行 commit：

```
QA-42 fix: 🐛 Fix crash on user logout
1. Ensure session is cleared before redirect
2. Add null check for auth token
```

```
SW-9 refactor: 💡 Simplify payment flow logic
1. Extract payment handler to service
2. Reduce nested conditions
```

```
QA-15 fix: 🐛 Fix form validation not triggering on blur
1. Add missing blur event binding on input field
2. Update validation service to handle empty state
```

### 其他範例：

```
SWD-33 chore: 🤖 Add Prettier config
```

```
QA-7 test: 💍 Add tests for user profile form
1. Cover input validation
2. Add edge case test for long names
```

```
SWD-99 fix: 🐛 Fix incorrect date format in report export
1. Update date pipe format from 'yyyy/MM/dd' to 'yyyy-MM-dd'
```

```
SWD-99 test: 💍 Add unit tests for report date formatting
1. Cover edge cases for different locales
2. Add test for empty date input
```

---

## 🔴 禁止事項

- 🔴 Commit 訊息不含 Jira 單號
- 🔴 Commit 類型與 Emoji 不匹配
- 🔴 一個 Commit 混雜多種不相關的修改
