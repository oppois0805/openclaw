---
name: layout-i18n
description: "多國語系 (i18n) 處理規範技能：定義 Keycode 命名規則（Common Code、State Code、Feature Code）與替換流程。包含：State Code 的特殊處理（stateCodeService）、HTML/TS 中的實作範例、以及禁止 Hardcode 文字的原則。適用於切版與邏輯開發階段，確保所有 UI 文字符合多國語系架構。"
---

# i18n 多國語系處理規範

本 Skill 定義了在開發過程中如何處理多國語系 (i18n) 的文字替換與 Keycode 命名規則。所有介面上的靜態文字與動態訊息皆須遵循此規範。

## 1. 核心原則

1.  **禁止 Hardcode 文字**：除測試資料外，所有 UI 顯示文字必須使用 i18n Keycode。
2.  **依據結構替換**：使用開發人員提供的語系結構進行 Keycode 替換。
3.  **Keycode 優先順序**：
    1.  **Common Code** (通用代碼)：跨功能通用的詞彙 (如：儲存、取消、計算依據)。
    2.  **State Code** (狀態代碼)：系統狀態、驗證訊息、錯誤訊息 (如：長度限制、必填)。
    3.  **Feature Code** (功能代碼)：該功能特有的詞彙。

## 2. Keycode 結構與範例

### 2.1 Common Code (通用代碼)

適用於標準化、重複出現的 UI 標籤或選項。

**命名格式**: `common_[category]_[index/name]`

| Key Code             | 狀態 | 畫面文字 (Default) | 分類      | 欄位說明 | 範例 (En/Tw/Cn/Ja)                        |
| :------------------- | :--- | :----------------- | :-------- | :------- | :---------------------------------------- |
| `common_calculate_1` | 啟用 | Based on           | Calculate |          | Based on / 計算依據 / 计算依据 / 計算基準 |
| `common_calculate_2` | 啟用 | Average            | Calculate |          | Average / 平均值 / 平均值 / 平均値        |

### 2.2 State Code (狀態代碼)

適用於系統驗證、錯誤提示或特定狀態描述，通常包含動態參數 (以 `*` 表示)。

**命名格式**: `S[Number]` (或其他專案定義格式)

| State Code | 狀態 | 介面 | 欄位說明           | 範例 (En/Tw/Cn/Ja)                                                                                                              |
| :--------- | :--- | :--- | :----------------- | :------------------------------------------------------------------------------------------------------------------------------ |
| `S01`      | 啟用 | 後台 | \*: 驗證欄位的限制 | Maximum length is * half width characters.<br>長度請勿超過*個半形字元<br>长度请勿超过*个字节<br>*半角文字を超えないでください。 |

### 2.3 Feature Code (功能代碼)

適用於特定功能頁面的專屬文字。

**命名格式**: `[feature_name]_[element]_[action/description]` (參考專案慣例)

## 3. 開發執行步驟

在 Layout 切版或 Spec 開發階段，遇到文字時：

1.  **識別文字類型**：
    - 是否為通用詞彙？ -> 查閱 Common Code 表。
    - 是否為驗證/訊息？ -> 查閱 State Code 表。
    - 若是功能專屬 -> 定義 Feature Code。

2.  **查找/詢問 Keycode**：
    - 若 Keycode 已提供，直接使用。
    - 若 Keycode 未提供，請詢問使用者或暫時依命名規則定義 (並註解 TODO)。

3.  **實作替換**：
    - **HTML 範例 (Template)**: `{{ 'common_calculate_1' | translate }}`
    - **TS 範例 (State Code)**: 使用 `this.stateCodeService.instant('S01')` (若需參數則傳入物件)。
    - **TS 範例 (Feature/Common)**: 使用 `this.translate.instant('key')`。

## 4. 檢查項目

- [ ] 所有靜態文字是否已替換為 Keycode？
- [ ] 帶有參數的訊息 (如 S01) 是否正確處理了參數替換？
- [ ] Keycode 是否使用了正確的分類 (Common vs Feature)？
