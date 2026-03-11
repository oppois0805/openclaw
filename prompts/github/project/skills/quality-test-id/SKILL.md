---
name: test-id
description: "Test ID 命名與自動產生：當需要為 HTML 元素添加 data-testid 屬性、為 E2E 測試準備識別器、設定自動化測試時使用。格式：prefixKeyForTestId + 描述 + '_' + 元素類型（content/button/input/tab/container/icon）。包含：IWA 元件 prefixKeyForTestId 設定規範、PrimeNG 元件 Test ID 配置、列舉使用原則（禁止硬編碼字串）、元素類型對照表、完整性檢查。適用於切版時添加 TestID、E2E 測試準備、自動化測試配置。"
---

# Test ID 產生

為 HTML 元素產生符合規範的 `data-testid` 屬性。

## 參考規範

執行此技能時**必須**完整理解以下規範：

| 規範                                         | 說明                           |
| :------------------------------------------- | :----------------------------- |
| [test_id.md](../../rules/test/test_id.md) | Test ID 完整命名規則與實作方式 |

## 基本格式

```html
<div
  [attr.data-testid]="testIdPrefix + MyIDs.Name + '_' + ElementType.Type"
></div>
```

### 格式組成

| 組成部分             | 說明                      | 必需性       |
| :------------------- | :------------------------ | :----------- |
| `prefixKeyForTestId` | 組件唯一識別 key          | 多實例時必需 |
| 描述                 | 元件描述，多字用 `-` 串接 | 必需         |
| 類型                 | 元件類型                  | 必需         |
| 流水號               | 從 1 開始的序號           | 視情況       |

## 元素類型對照

| 元素功能   | 使用類型    |
| :--------- | :---------- |
| 純顯示內容 | `content`   |
| 點擊互動   | `button`    |
| 資料輸入   | `input`     |
| 標籤切換   | `tab`       |
| 容器包裝   | `container` |
| 圖示       | `icon`      |

## 什麼元素需要 TestID？

| 元素類型   | 是否需要  |
| :--------- | :-------- |
| 內容元素   | ✅ 需要   |
| 互動元素   | ✅ 需要   |
| 功能性容器 | ✅ 需要   |
| 純排版容器 | ❌ 不需要 |
| 第三方組件 | ✅ 需要   |

## 禁止事項

- 🔴 在 HTML 範本中呼叫函式產生 TestID
- 🔴 硬編碼字串，應使用列舉組合
- 🔴 描述中包含類型名稱（如 `save-button_button`）
