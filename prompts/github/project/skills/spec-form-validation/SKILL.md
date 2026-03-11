---
name: form-validation
description: "表單驗證開發技能：當處理 Angular Reactive Forms 表單驗證、實作驗證規則、設定錯誤訊息時使用。支援：1⃣ 內建驗證器（required/email/pattern/min/max）、2⃣ 自訂同步驗證器、3⃣ 非同步驗證器（API 檢查）、4⃣ 跨欄位驗證器（比對檢查）。工作流程：確認需求 → 檢查專案現有 Validators → 優先使用內建/重用 → 必要時才建新。適用於表單開發、驗證規則實作、錯誤訊息處理。"
---

# Form Validation

實作 Angular Reactive Forms 表單驗證邏輯。

## 官方文件

- [Form Validation](https://angular.dev/guide/forms/form-validation)
- [Reactive Forms](https://angular.dev/guide/forms/reactive-forms)
- [Validators API](https://angular.dev/api/forms/Validators)

## 工作流程

### 1. 確認需求來源

依據提供的需求文件識別驗證規則。

### 2. 檢查專案現有 Validators

在建立新的 validator 之前，**必須**先搜尋專案中是否已存在可重用的 validator：

1. **搜尋專案中的 Validator 檔案**
   - 搜尋 `*.validator.ts` 或 `*validators*.ts` 檔案
   - 搜尋包含 `ValidatorFn` 或 `AsyncValidatorFn` 的檔案
   - 檢查 `shared` 或 `common` 資料夾下的驗證器

2. **搜尋關鍵字**
   - 依據需求的驗證類型搜尋相關關鍵字（如 `email`、`phone`、`required`、`pattern` 等）
   - 搜尋 `Validators.` 使用情況以了解專案慣例

3. **判斷是否可重用**
   - 若已有符合的 validator，直接重用
   - 若現有 validator 接近需求，評估是否可擴展
   - 確認無可用的 validator 後，才進行新開發

### 3. 實作驗證邏輯

- 使用 Angular 內建 Validators 優先
- 重用專案現有的自訂 Validators
- 若需建立新的 Validator，參考下方參考資料

## 參考資料

| 主題         | 參考文件                                                                     |
| ------------ | ---------------------------------------------------------------------------- |
| 驗證類型總覽 | [references/validator-types.md](references/validator-types.md)               |
| 同步驗證器   | [references/sync-validators.md](references/sync-validators.md)               |
| 非同步驗證器 | [references/async-validators.md](references/async-validators.md)             |
| 跨欄位驗證器 | [references/cross-field-validators.md](references/cross-field-validators.md) |
| 錯誤訊息處理 | [references/error-messages.md](references/error-messages.md)                 |
