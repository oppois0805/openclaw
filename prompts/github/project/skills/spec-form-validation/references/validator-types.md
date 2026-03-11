# 驗證類型

> 參考: https://angular.dev/api/forms/Validators

## 內建驗證器

| 驗證類型 | Angular Validator | 說明 |
|----------|-------------------|------|
| 必填 | `Validators.required` | 欄位不可為空 |
| 必須為 true | `Validators.requiredTrue` | 常用於 checkbox |
| 最小長度 | `Validators.minLength(n)` | 最少 n 個字元 |
| 最大長度 | `Validators.maxLength(n)` | 最多 n 個字元 |
| 正規表達式 | `Validators.pattern(regex)` | 符合特定格式 |
| Email | `Validators.email` | Email 格式 |
| 數值最小 | `Validators.min(n)` | 數值 ≥ n |
| 數值最大 | `Validators.max(n)` | 數值 ≤ n |

## 自訂驗證器

當內建驗證器無法滿足需求時，可建立自訂驗證器：

| 類型 | 介面 | 使用時機 |
|------|------|----------|
| 同步驗證器 | `ValidatorFn` | 不需 API 的驗證 |
| 非同步驗證器 | `AsyncValidatorFn` | 需呼叫 API 的驗證 |
| 跨欄位驗證器 | `ValidatorFn` | 多欄位關聯驗證 |

## 常見跨欄位驗證場景

| 場景 | 說明 |
|------|------|
| 密碼確認 | 兩個密碼欄位必須相同 |
| 日期範圍 | 結束日期必須大於開始日期 |
| 條件必填 | 當欄位 A 有值時，欄位 B 必填 |
